# Q18 - Διαθεσιμότητα και ανάκαμψη

**Πηγή:** [Q18 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** High Availability and Disaster Recovery

Το λειτουργικό AKS ανάπτυξης δεν αποδεικνύει συνεχή παραγωγική διαθεσιμότητα ή ανάκαμψη περιοχής.

[HTML διάγραμμα](q18-ha-dr.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] Ανάπτυξη / UAT [DEV]
  +-- άλλη λειτουργία / στόχος --> [n2] Συνεχής παραγωγή [DESIGNED]
  |   +-- ανθεκτικότητα / στόχος --> [n3] Βάσεις και workflows [DESIGNED]
  |   |   +-- σχέδιο DR / στόχος --> [n4] Δευτερεύουσα περιοχή [DESIGNED]
  |   |   |   +-- δοκιμή / στόχος --> [n5] Δοκιμή ανάκαμψης [REQUIRED]

[n6] RTO / RPO [PENDING]
  +-- κριτήρια / στόχος --> [n5] Δοκιμή ανάκαμψης [REQUIRED] (αναφορά)
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | Ανάπτυξη / UAT | AKS με σκόπιμο stop/start | DEV (ανάπτυξη) |
| n2 | Συνεχής παραγωγή | Πολλαπλά replicas / ζώνες | DESIGNED (σχεδιασμένο) |
| n3 | Βάσεις και workflows | HA PostgreSQL / Temporal | DESIGNED (σχεδιασμένο) |
| n4 | Δευτερεύουσα περιοχή | Ανάκαμψη δεδομένων και ρυθμίσεων | DESIGNED (σχεδιασμένο) |
| n5 | Δοκιμή ανάκαμψης | Συνολική εφαρμογή και εξαρτήσεις | REQUIRED (απαίτηση) |
| n6 | RTO / RPO | Εγκεκριμένοι στόχοι πελάτη | PENDING (εκκρεμές) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | άλλη λειτουργία | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n2 -> n3 | ανθεκτικότητα | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n3 -> n4 | σχέδιο DR | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n4 -> n5 | δοκιμή | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n6 -> n5 | κριτήρια | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |

## Τεκμηρίωση και όρια

- HA (υψηλή διαθεσιμότητα) και DR (ανάκαμψη καταστροφής) απαιτούν διαφορετικά τεκμήρια.
- Η αναφορά PostgreSQL σε RPO 0 και συνήθη μετάπτωση 60-120s αφορά τη βάση, όχι εγγυημένο RTO (χρόνο ανάκαμψης) εφαρμογής.
- Backup/restore 24h/48h, warm standby 1h/4h και active/active 0/<15min είναι μη δοκιμασμένοι στόχοι RPO/RTO (απώλειας δεδομένων/ανάκαμψης).
- Ολοκληρωμένη δοκιμή καλύπτει γνώση, Temporal, ταυτότητες, δίκτυο και πάροχο μοντέλου. Redis δεν κρατά αναντικατάστατα επίσημα δεδομένα.

## Υπόμνημα

- DEV (ανάπτυξη)
- DESIGNED (σχεδιασμένο)
- REQUIRED (απαίτηση)
- PENDING (εκκρεμές)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** Production HA and DR are designed separately from the current development/UAT operating
mode: the cluster already exists and NCT-AI workloads have been deployed and observed running, and being
stopped when idle in the current cost-optimised non-production mode is a cost-management decision, not a
failure of HA design — Production needs a different operating posture of continuously running capacity,
multi-replica workloads, zonal resilience where supported, monitored failover and tested DR. AKS:
production Standard/Premium tier, multiple availability zones where the region supports them, at least
3 nodes, multiple pod replicas, topology spread/anti-affinity, PodDisruptionBudgets, HPA,
readiness/liveness/startup probes, rolling deployment, zone-aware storage — the next step is not
"deploy to AKS" (already demonstrated in namespace `nct-ai`) but proving replica distribution, zone
behaviour, failover and recovery under the final Production topology. PostgreSQL/pgvector/AGE: Azure
Database for PostgreSQL Flexible Server with zone-redundant HA, PITR/backups, and geo-redundant backup
or cross-region read replica per DR requirements. Microsoft's HA figures concern the database:
RPO 0 for committed transactions and typical failover of 60–120 seconds, which can take longer under
load. They are not a hard 120-second bound or the application's end-to-end RTO; reconnects, workers,
queues and dependent services need separate recovery tests, as does regional DR.
[PostgreSQL HA documentation](https://learn.microsoft.com/en-us/azure/postgresql/high-availability/concepts-high-availability).
Temporal: self-hosted with multiple
Frontend/History/Matching replicas and HA PostgreSQL persistence, or Temporal Cloud as the managed
alternative. Redis: Azure Managed Redis, holding no authoritative data that cannot be reconstructed.
Model provider: primary plus controlled fallback deployment with timeout/circuit-breaker for Azure, or
N+1 replicated capacity with a load balancer for a local option. DR: a primary-region topology (AKS,
PostgreSQL HA, Managed Redis, storage, model endpoint) replicating configuration/data to a secondary
region on warm standby; an initial discussion target of near-zero RPO for critical
configuration/knowledge artifacts (already replicated/source-controlled), a customer-selected RPO for
operational PostgreSQL, and an RTO target in hours rather than days for platform recovery. Final RTO/RPO
require Telekom Business Continuity approval.

The new response supplies three options for discussion: backup/restore at RPO 24h and RTO 48h;
warm standby at RPO 1h and RTO 4h; and active/active aiming at RPO 0 and RTO under 15 minutes.
These are untested business targets, not properties obtained merely by choosing those topologies.
In particular, RPO 0 across regions requires a demonstrated replication/consistency design for every
authoritative store; zonal database HA does not establish it. Select a tier and test the complete
recovery path, including knowledge, Temporal state, identities, networking and model availability.

</details>
