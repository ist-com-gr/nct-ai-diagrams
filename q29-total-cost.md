# Q29 - Συνολικό κόστος χωρίς διπλομέτρηση

**Πηγή:** [Q29 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** Licensing, support, recurring costs, and total TCO

Δεν υπάρχει εγκεκριμένη παραγωγική βάση TCO. Οι εμπορικές εκτιμήσεις δεν είναι πλήρες κόστος ιδιοκτησίας.

[HTML διάγραμμα](q29-total-cost.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] Φάσεις και setup [ESTIMATE]
  +-- αν πρόσθετα --> [n2] Δύο έτη υποστήριξης [ASSUMPTION]
  |   +-- άθροισμα --> [n3] Υποσύνολο [ESTIMATE]
  |   |   +-- συν εξαιρέσεις --> [n4] Πραγματικό TCO [PENDING]

[n6] Μετρημένοι όγκοι [REQUIRED]
  +-- επικύρωση / στόχος --> [n5] Λειτουργία παραγωγής [ESTIMATE]
  |   +-- προστίθεται --> [n4] Πραγματικό TCO [PENDING] (αναφορά)
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | Φάσεις και setup | EUR 810K-1.07M | ESTIMATE (εκτίμηση) |
| n2 | Δύο έτη υποστήριξης | Επιπλέον EUR 160-240K | ASSUMPTION (παραδοχή) |
| n3 | Υποσύνολο | EUR 970K-1.31M | ESTIMATE (εκτίμηση) |
| n4 | Πραγματικό TCO | Φόροι / runtime / εξαιρέσεις | PENDING (εκκρεμές) |
| n5 | Λειτουργία παραγωγής | EUR 15-30K / μήνα αιχμής | ESTIMATE (εκτίμηση) |
| n6 | Μετρημένοι όγκοι | UAT / tokens / συμβάσεις | REQUIRED (απαίτηση) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | αν πρόσθετα | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n2 -> n3 | άθροισμα | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n3 -> n4 | συν εξαιρέσεις | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n5 -> n4 | προστίθεται | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n6 -> n5 | επικύρωση | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |

## Τεκμηρίωση και όρια

- ESTIMATE (εκτίμηση), όχι προσφορά. Το EUR 900K-1.1M της πηγής δεν αποτελεί πλήρες τριετές TCO.
- 36 μήνες σταθερής αιχμής δίνουν EUR 540K-1.08M runtime (λειτουργίας), μόνο αριθμητικό παράδειγμα, όχι πρόβλεψη αύξησης.
- Επιβεβαιώνονται επικαλύψεις φάσεων, συμβάσεις υποστήριξης, άδειες, DR (ανάκαμψη), φόροι, εκπτώσεις και χρόνοι διάθεσης.
- Τα οφέλη C#/διακυβέρνησης διαχωρίζονται από πρόσθετα οφέλη LLM. Ο προτεινόμενος τρισκελής συγκριτικός προϋπολογισμός δεν έχει ακόμη παραχθεί.

## Υπόμνημα

- ESTIMATE (εκτίμηση)
- ASSUMPTION (παραδοχή)
- PENDING (εκκρεμές)
- REQUIRED (απαίτηση)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** A credible TCO figure needs the target Azure region, AKS SKU/node families, UAT and
Production availability requirements, PostgreSQL compute/storage, DR region, log retention, model
choice, token volume, pay-as-you-go vs provisioned throughput, Temporal self-hosted vs Cloud, support
contracts and any on-prem GPU option fixed first; a complete approved Production cost baseline is not
yet available. The ledger combines provider-reported usage where available with **estimated** cost
from a versioned price table, not freshly verified rates or a reconciled bill (Appendix B6).
Actual expenditure requires Azure billing/Cost Management data, contractual discounts,
capacity charges and reconciliation. Annual projections also need measured UAT volumes and documented
assumptions; neither a per-call estimate nor a historical retail rate establishes total TCO. Temporal's
software cost is genuinely zero if self-hosted (MIT-licensed server); the operational cost of running it
is not zero and is not yet estimated. The existing Azure AKS environment is intentionally stopped when
not in use, which materially reduces non-production compute cost while some supporting Azure services
continue to bill regardless — Production should not rely on stop/start as a cost-control mechanism,
because its HA/SLO posture differs. Cost buckets to model: AKS/compute, PostgreSQL, Redis, storage,
observability, Azure OpenAI/Foundry usage (input + output + embedding tokens, plus any provisioned
capacity), Temporal (software cost vs Cloud subscription), CI/CD and security tooling (license
entitlement and any incremental cost require confirmation), and DR (which materially increases TCO
through warm capacity, replicated data and redundant endpoints). The recommended next deliverable, not yet produced, is a
three-scenario cost model — Azure AI cost-optimised, Azure AI production HA/enterprise, and on-prem
GPU — priced from measured UAT tokens and requests rather than theoretical prompts.

**Commercial proposal from the additional response, not an approved quotation.** These numbers are
source-provided estimates, not newly researched market prices or a delivery commitment:

| Item | Proposed duration / scope | Indicative amount |
|---|---|---:|
| Phase 1 | Approximately 6 months; AGG-01 advisory-only pilot, no activated write path | EUR 180–220K development |
| Initial infrastructure setup | Provisioning effort; runtime billed separately | EUR 30–50K |
| Phase 2 | Approximately 9–12 months; AGG-05, AGG-03, MW-01, AGG-04, AGG-06 | EUR 350–450K |
| Phase 3 | Years 2–3; RAN-01, RAN-03, RAN-05, RAN-06 and AI-assisted licensing-table work | EUR 250–350K |
| Support after Year 1 | Year 1 proposed as included; coverage/hours and exclusions unconfirmed | EUR 80–120K/year |
| Runtime at Production peak | Azure infrastructure and model usage/capacity | EUR 15–30K/month |
| Source's stated three-year development/support envelope | Not an all-inclusive TCO | EUR 900K–1.1M |

**Arithmetic reconciliation is required.** Phases plus setup sum to EUR 810K–1.07M. If two subsequent
support years are additive, the subtotal becomes EUR 970K–1.31M, before runtime, tax and any excluded
items. Separately, 36 months at the stated peak runtime rate would be EUR 540K–1.08M; this is only a
constant-peak illustration, not a ramp-up forecast. The quoted EUR 900K–1.1M therefore cannot be used
as total three-year ownership cost. Confirm overlap/inclusions, staffing, acceptance criteria,
environments, rollout timing, discounts and support coverage before consolidating the budget. Neither
an existing Enterprise Agreement nor open-source licensing proves there are no additional licence costs.

The business case should separate the AGG-01 rules/governance baseline from optional LLM features.
Reading structured fields, applying fixed checks and showing a result is not by itself a reason to
require model inference. Budget and justify explanation, chat and verification calls separately using
the non-LLM comparisons in Q21; do not count deterministic automation gains as proven LLM gains.

</details>
