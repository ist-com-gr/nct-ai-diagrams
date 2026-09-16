# Q17 - Καταγραφή ελέγχου και αναπαραγωγή

**Πηγή:** [Q17 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** End-to-end audit trail and root-cause debugging of wrong recommendations

Το audit trail (ίχνος ελέγχου) συνδέει τεκμήρια· δεν ανακατασκευάζει διαγραμμένα δεδομένα ούτε εγγυάται ίδια έξοδο LLM.

[HTML διάγραμμα](q17-audit-and-replay.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] Εκτέλεση και ταυτότητα [PARTIAL]
  +-- καταγραφή --> [n2] Καταγραφή και συσχέτιση [BUILT]
  |   +-- διερεύνηση --> [n3] Διερεύνηση αιτίας [REQUIRED]
  |   |   +-- αναπαραγωγή / στόχος --> [n4] Αναπαραγωγή C# [CONDITIONAL]

[n6] Πολιτική διατήρησης [REQUIRED]
  +-- επιτρέπει --> [n5] Αμετάβλητα στιγμιότυπα [REQUIRED]
  |   +-- ίδιες είσοδοι --> [n4] Αναπαραγωγή C# [CONDITIONAL] (αναφορά)
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | Εκτέλεση και ταυτότητα | Χρήστης / run / trace | PARTIAL (μερικώς υλοποιημένο) |
| n2 | Καταγραφή και συσχέτιση | Chat persistence / Trace view | BUILT (υλοποιημένο) |
| n3 | Διερεύνηση αιτίας | Εκδόσεις και αποτελέσματα | REQUIRED (απαίτηση) |
| n4 | Αναπαραγωγή C# | Ίδια δεδομένα / γνώση / κώδικας | CONDITIONAL (υπό όρους) |
| n5 | Αμετάβλητα στιγμιότυπα | Αρχική είσοδος και εκδόσεις | REQUIRED (απαίτηση) |
| n6 | Πολιτική διατήρησης | Περιεχόμενο μόνο όπου επιτρέπεται | REQUIRED (απαίτηση) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | καταγραφή | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n2 -> n3 | διερεύνηση | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n3 -> n4 | αναπαραγωγή | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n5 -> n4 | ίδιες είσοδοι | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n6 -> n5 | επιτρέπει | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |

## Τεκμηρίωση και όρια

- Υπάρχει μόνιμη καταγραφή συνομιλίας/κλήσεων· δεν τεκμηριώνεται πλήρης κάλυψη κάθε υπηρεσίας και χρήστη.
- Hashes (αποτυπώματα) και IDs (αναγνωριστικά) δίνουν ακεραιότητα/συσχέτιση, όχι διαγραμμένο περιεχόμενο.
- Temporal replay (αναπαραγωγή ιστορικού) δεν εγγυάται ίδια νέα ανάγνωση Oracle ή ίδια νέα inference (εκτέλεση μοντέλου).
- Διακρίνονται απόφαση C#, έγκριση γνώσης, διατύπωση LLM, ετυμηγορία Verify και ανθρώπινη έγκριση. Έλλειψη τεκμηρίων δηλώνεται.

## Υπόμνημα

- PARTIAL (μερικώς υλοποιημένο)
- BUILT (υλοποιημένο)
- REQUIRED (απαίτηση)
- CONDITIONAL (υπό όρους)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** **Mandatory Production/UAT requirement, not yet complete at every hop.** The target audit
record links user/role/session, execution and trace IDs, intent/agent/policy versions, fact and tool
references, pinned knowledge version, executable rule version/results, prompt/model versions, usage,
evidence, final recommendation and feedback. A durable queryable trail exists in `NCT.AI.Chat`
and the AG-UI Trace view exposes correlation; full cross-service coverage and user attribution still
need verification (Q25).

Auditability is not identical to exact replay. Identifiers and hashes establish correlation and
integrity but cannot reconstruct deleted facts or model content. Reading Oracle again can return
different facts. Deterministic reproduction requires retained input snapshots or immutable fact
artifacts, the originally pinned knowledge, and compatible rule/application versions. Model inputs and
outputs can only be inspected where capture and retention policy permit; calling the same model again
does not guarantee the same answer. Temporal history replays recorded workflow decisions, not a
guarantee that a newly executed external read or inference reproduces the original result.

Investigation therefore first establishes which evidence remains available, inspects the recorded
versions and outcomes, distinguishes original facts from fresh observations, classifies the cause and
adds a regression case. Missing or expired evidence must be reported as a reproducibility limitation,
not reconstructed from hashes or silently replaced with current data.

The additional response's statement that no environment has any durable audit storage is too broad:
chat persistence and model invocation records must be distinguished from incomplete platform-wide
audit coverage. A metadata-only ledger also cannot show the original model answer without an approved,
retained content artifact.

Record the distinction between rule decision, knowledge approval, model-authored explanation,
verifier verdict and engineer approval. An approved knowledge pin establishes which source version was
used, not that the C# rule faithfully implements its meaning; that needs the consistency checks in Q6.

</details>
