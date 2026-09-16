# Q03 - Διαστασιολόγηση προς επικύρωση

**Πηγή:** [Q3 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** UAT and Production sizing/capacity tables

Μία προτεινόμενη βάση UAT/παραγωγής, με τελική διαστασιολόγηση μόνο μετά από δοκιμές πραγματικού φόρτου.

[HTML διάγραμμα](q03-capacity.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] Παραδοχές φόρτου [PROPOSAL]
  +-- παραδοχές / στόχος --> [n2] UAT βάση [PROPOSAL]
  |   +-- κλιμάκωση / στόχος --> [n3] Παραγωγή βάση [PROPOSAL]
  |   |   +-- όχι έγκριση / στόχος --> [n4] Τελική διαστασιολόγηση [PENDING]

[n6] Χωρητικότητα παρόχου [REQUIRED]
  +-- όρια / στόχος --> [n5] UAT μετρήσεις [REQUIRED]
  |   +-- επικύρωση / στόχος --> [n4] Τελική διαστασιολόγηση [PENDING] (αναφορά)
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | Παραδοχές φόρτου | 1.000 χρήστες / 20 γεννήσεις | PROPOSAL (πρόταση) |
| n2 | UAT βάση | AKS 24-32 vCPU / 96-128 GB | PROPOSAL (πρόταση) |
| n3 | Παραγωγή βάση | AKS 48-64 vCPU / 192-256 GB | PROPOSAL (πρόταση) |
| n4 | Τελική διαστασιολόγηση | Μετά από μετρήσεις | PENDING (εκκρεμές) |
| n5 | UAT μετρήσεις | Ουρές / p95 / πόροι | REQUIRED (απαίτηση) |
| n6 | Χωρητικότητα παρόχου | RPM / TPM / tokens | REQUIRED (απαίτηση) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | παραδοχές | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n2 -> n3 | κλιμάκωση | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n3 -> n4 | όχι έγκριση | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n6 -> n5 | όρια | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n5 -> n4 | επικύρωση | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |

## Τεκμηρίωση και όρια

- PROPOSAL (πρόταση), όχι εγκεκριμένη προμήθεια. Περίπου 100 ενεργοί χρήστες αιχμής και τυπικό context (περιεχόμενο εισόδου) 32K tokens.
- PostgreSQL UAT: 4-8 vCPU, 32-64 GB, 256-512 GB. Παραγωγή: 8-16 vCPU, 64-128 GB, 0.5-1 TB.
- Η πρόταση 2028+ βρίσκεται μόνο στο Παράρτημα A της πηγής ως εναλλακτική υπό αξιολόγηση. Οι πλήρεις πίνακες παραμένουν στην Q3.
- GPU inference (εκτέλεση μοντέλου) και RPM/TPM (αιτήματα/tokens ανά λεπτό) μετρώνται χωριστά από CPU/RAM AKS.

## Υπόμνημα

- PROPOSAL (πρόταση)
- PENDING (εκκρεμές)
- REQUIRED (απαίτηση)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** The tables below are the **recommended planning baseline**, not final procurement sizing — final values
need validation from performance/load testing against the real AGG-01 and subsequent use cases.
Planning assumptions: ~1,000 registered users, ~100 peak active users, ~20 concurrent LLM generations,
typical context ~32K tokens, multiple agent/model calls per complex request, embeddings enabled,
production HA required. Where Azure OpenAI is used, model-inference GPU capacity sits outside the
NCT-AI AKS CPU/RAM sizing below.

**UAT baseline**

| Component | CPU | RAM | Storage | Growth / notes |
|---|---:|---:|---:|---|
| AKS system + application workloads | 24–32 vCPU baseline | 96–128 GB | 200–300 GB ephemeral/platform | scale with test concurrency |
| PostgreSQL / pgvector / AGE | 4–8 vCPU | 32–64 GB | 256–512 GB | plan 20–30% annual logical growth initially |
| Temporal persistence/workers | shared or dedicated | 8–16 GB incremental if dedicated | 100–200 GB history budget | retention must be configured |
| Redis | managed service | 2–6 GB cache | non-authoritative | cache only |
| Knowledge/object artifacts | n/a | n/a | 250–500 GB | manuals, OKF, indexes, test sets |
| Logs / traces | n/a | n/a | policy-driven | shorter retention in UAT |

Recommended UAT topology: a minimum 3-node resilient cluster, separate system/application node pools
where practical, Horizontal Pod Autoscaling, and non-production but production-like network/security
controls.

**Production baseline**

| Component | CPU | RAM | Storage | Growth / notes |
|---|---:|---:|---:|---|
| AKS application baseline | 48–64 vCPU | 192–256 GB | 300–500 GB platform | autoscale to 96–128 vCPU / 384–512 GB at peak |
| PostgreSQL / pgvector / AGE | 8–16 vCPU | 64–128 GB | 0.5–1 TB initial | measured per bundle/conversation/audit policy |
| Temporal services/workers | horizontally scaled | workload-dependent | HA persistence | multiple replicas, per-workload queues |
| Azure Managed Redis | managed HA tier | by cache profile | non-authoritative | sized from session/cache hit rate |
| Knowledge/object storage | n/a | n/a | 0.5–1 TB initial | versioned manuals, bundles, artifacts |
| Audit/log/trace | n/a | n/a | potentially 1+ TB/year | depends heavily on payload retention policy |

These figures are deliberately conservative planning ranges, to be validated against request rate,
document corpus size, embedding chunk count, conversation retention, audit payload size, Temporal
history retention, concurrent tool calls and actual rule-execution load. On-prem model inference needs
a separate GPU capacity table, driven by model choice, precision/quantisation, context length,
concurrency and required tokens/sec — it cannot be derived from the AKS figures alone.

Provider capacity also needs model- and environment-specific RPM/TPM quotas (requests/tokens per
minute), request arrival rate, input/output token distributions, calls per workflow, retries, and
p50/p95/p99 latency under an agreed workload. Twenty concurrent generations and a 32K context alone
cannot establish throughput or cost. UAT must record quota throttling and queueing as well as AKS
resource use; no validated Production quota or capacity guarantee is asserted here.

**Target / remaining gap.** Validate this single recommended baseline in UAT before procurement.
The alternative 2028+ proposal is retained only in Appendix A as a scenario under evaluation, not a
second recommendation or an approved capacity commitment.

</details>
