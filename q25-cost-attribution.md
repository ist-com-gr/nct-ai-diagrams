# Q25 - Χρήση, εκτίμηση και πραγματική χρέωση

**Πηγή:** [Q25 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** Monitoring and limiting LLM consumption/cost per user, use case and environment

Το invocation ledger (μητρώο κλήσεων) είναι υλοποιημένο, όχι πλήρες σύστημα κατανομής κόστους ή επιβολής προϋπολογισμού.

[HTML διάγραμμα](q25-cost-attribution.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] Κλήση μοντέλου [BUILT]
  +-- καταγραφή --> [n2] Invocation ledger [BUILT]
  |   +-- υπολογισμός --> [n3] Εκτίμηση τιμοκαταλόγου [BUILT]
  |   |   +-- συμφωνία / στόχος --> [n4] Πραγματική χρέωση [PENDING]
  |   +-- ταυτοποίηση / στόχος --> [n5] Απόδοση κόστους [INCOMPLETE]

[n6] Όρια δαπάνης [DESIGNED]
  +-- προϋπολογισμός / στόχος --> [n5] Απόδοση κόστους [INCOMPLETE] (αναφορά)
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | Κλήση μοντέλου | Χρήση από πάροχο όπου υπάρχει | BUILT (υλοποιημένο) |
| n2 | Invocation ledger | Μητρώο κλήσεων / πληρότητα | BUILT (υλοποιημένο) |
| n3 | Εκτίμηση τιμοκαταλόγου | Εκδομένη τιμή, όχι τιμολόγιο | BUILT (υλοποιημένο) |
| n4 | Πραγματική χρέωση | Συμφωνία με Azure billing | PENDING (εκκρεμές) |
| n5 | Απόδοση κόστους | Χρήστης / σενάριο / περιβάλλον | INCOMPLETE (μη ολοκληρωμένο) |
| n6 | Όρια δαπάνης | Άρνηση / ουρά / υποβάθμιση | DESIGNED (σχεδιασμένο) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | καταγραφή | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n2 -> n3 | υπολογισμός | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n3 -> n4 | συμφωνία | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n2 -> n5 | ταυτοποίηση | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n6 -> n5 | προϋπολογισμός | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |

## Τεκμηρίωση και όρια

- Ορισμένες κλήσεις δείχνουν υπηρεσία αντί τελικού μηχανικού. Χρειάζεται συνεπής διάδοση ταυτότητας και execution/trace IDs (αναγνωριστικών εκτέλεσης/ίχνους).
- Μετρημένη χρήση και εκτιμώμενο κόστος παραμένουν διακριτά. Ελλείπουσα χρήση διατηρεί ένδειξη προέλευσης/πληρότητας.
- Alerts (ειδοποιήσεις) και provisioned throughput (δεσμευμένη ρυθμαπόδοση) δεν αποτελούν πλήρες hard budget (αυστηρό όριο δαπάνης).
- Η παραδοχή 50% των tokens για AGG-05 χρειάζεται μέτρηση. Οι έλεγχοι πρέπει να καλύπτουν και εναλλακτικές διαδρομές.

## Υπόμνημα

- BUILT (υλοποιημένο)
- PENDING (εκκρεμές)
- INCOMPLETE (μη ολοκληρωμένο)
- DESIGNED (σχεδιασμένο)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** Usage recording exists, but it is not a complete billing-attribution or budget-enforcement
system.

**Current state: BUILT.** The invocation ledger records usage and price-based estimates. End-user,
role and environment attribution is not guaranteed on every invocation; some paths identify the
calling service rather than the initiating engineer. Token usage is measured where the provider
reports it, with source/completeness recorded for missing or estimated values. Price-based cost
remains an estimate, not a reconciled Azure charge (Appendix B6).

**DESIGNED / incomplete:** complete per-user/use-case/environment cost reporting and budget enforcement.
Before a FinOps dashboard can provide reliable attribution, callers must consistently propagate
authenticated user or service identity, environment, use case, execution and trace correlation.
A chart over the current tables alone cannot fill missing identity data.

The target controls include separate environment/model quotas and budgets, per-user/role rate and
concurrency limits, per-use-case input/output and model-call budgets, permitted model classes,
and retrieval/context limits. Their enforcement and denial behaviour must be tested independently;
the ledger proves recording capability, not that all budget controls are active.

The source's assumption that AGG-05 consumes approximately 50% of tokens needs measurement. Provisioned
throughput is a capacity choice, not a complete spend cap; alerts are not request-denial controls.
Any hard budget must define whether new requests are refused, queued or degraded, cover fallback
routes, and be reconciled with actual billing. No such universal enforcement is asserted here.

</details>
