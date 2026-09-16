# Q24 - Αρχικοί στόχοι επιδόσεων UAT

**Πηγή:** [Q24 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** Target response times and maximum interactive latency

Οι τιμές p95 είναι προτεινόμενοι στόχοι σχεδιασμού, όχι μετρημένα SLOs ή εγγυημένοι μέγιστοι χρόνοι.

[HTML διάγραμμα](q24-performance.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] Ορισμός φόρτου [REQUIRED]
  +-- συνθήκες / στόχος --> [n2] Υποψήφιοι στόχοι p95 [PROPOSAL]
  |   +-- επικύρωση / στόχος --> [n3] Μετρήσεις UAT [PENDING]
  |   |   +-- τεκμήρια / στόχος --> [n4] Συμφωνία SLO [PENDING]

[n6] Work Order DryRun [PROPOSAL]
  +-- όχι write SLA / στόχος --> [n5] Timeout / async [DESIGNED]
  |   +-- χωριστή απόφαση / στόχος --> [n4] Συμφωνία SLO [PENDING] (αναφορά)
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | Ορισμός φόρτου | Αιτήματα / μήκη / concurrency | REQUIRED (απαίτηση) |
| n2 | Υποψήφιοι στόχοι p95 | Αρχικές τιμές συζήτησης | PROPOSAL (πρόταση) |
| n3 | Μετρήσεις UAT | p50 / p95 / p99 / ουρές | PENDING (εκκρεμές) |
| n4 | Συμφωνία SLO | Στόχος επιπέδου υπηρεσίας | PENDING (εκκρεμές) |
| n5 | Timeout / async | Όριο αναμονής / ασύγχρονη ροή | DESIGNED (σχεδιασμένο) |
| n6 | Work Order DryRun | 2s p95 πρόταση προεπισκόπησης | PROPOSAL (πρόταση) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | συνθήκες | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n2 -> n3 | επικύρωση | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n3 -> n4 | τεκμήρια | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n5 -> n4 | χωριστή απόφαση | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n6 -> n5 | όχι write SLA | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |

## Τεκμηρίωση και όρια

- AGG-05 πρώτο token 2s, πλήρης απάντηση 10s· AGG-01 απόφαση χωρίς εξήγηση 4s· MW-02 <10s· μαζική επεξεργασία 5s ανά σταθμό.
- Έλεγχος συνέπειας: ασύγχρονος, στόχος ολοκλήρωσης προς συμφωνία. Οι τιμές δεν αποδεικνύουν λειτουργικό σενάριο.
- Work Order 2s αφορά μόνο DryRun (δοκιμαστική εκτέλεση χωρίς εγγραφή), μετά την ανθρώπινη έγκριση. Πραγματική εγγραφή θέλει άλλο στόχο.
- Timeout (όριο αναμονής) δεν εγγυάται ολοκλήρωση. Το 202 Accepted παραμένει στόχος σύμβασης, όχι καθολικά υλοποιημένη συμπεριφορά.

## Υπόμνημα

- REQUIRED (απαίτηση)
- PROPOSAL (πρόταση)
- PENDING (εκκρεμές)
- DESIGNED (σχεδιασμένο)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** The table contains **initial performance objectives to be validated in UAT**. These
candidate p95 design objectives are discussion inputs, not measured SLOs, contractual SLAs or
guaranteed maximum response times. They do not establish that the scenarios are operational.

| Use case / measurement | Candidate p95 objective | Scope / qualification |
|---|---:|---|
| AGG-05 chat, first token | 2s | Define whether authentication, retrieval and tool work are included |
| AGG-05 chat, complete streamed answer | 10s | Fix input/output length and tool-call workload |
| AGG-01 deterministic PoTP recommendation | 4s | Core decision, excluding optional model explanation |
| MW-02 link availability query | <10s | Validate against the actual integration |
| Mass generation, per-site processing | 5s | Individual site samples; also measure total batch duration |
| Consistency run | Not yet set | Asynchronous progress/status and completion objective to agree |
| Work Order DryRun preview, after approval | 2s | No persisted NCTSite write; excludes human review time |

**Current state.** No benchmark results establishing these values are asserted here. A DryRun result
cannot establish actual-write latency; any future authorised write path needs its own acceptance
objective and tests (Q16).

**Target / remaining gap.** UAT must measure p50/p95/p99 under agreed arrival rates, concurrency,
input/output lengths, provider RPM/TPM, queues and retries (Q3). Agree timeouts and asynchronous
transition thresholds separately; a timeout is not a guarantee of completion. Longer workflows
should use durable jobs with run IDs and progress. A `202 Accepted` response remains a target API
contract, not a verified behaviour of every scenario endpoint.

</details>
