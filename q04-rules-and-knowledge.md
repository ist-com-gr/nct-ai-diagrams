# Q04 - C# και εγκεκριμένη γνώση

**Πηγή:** [Q4 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** Relationship between the Approved OKF Knowledge Bundle and executable deterministic rules

Η C# εκτελεί ελέγχους· το OKF τεκμηριώνει την εγκεκριμένη γνώση. Δεν υπάρχει αυτόματη μεταγλώττιση εγχειριδίων σε κανόνες C#.

[HTML διάγραμμα](q04-rules-and-knowledge.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] NCTSite / Oracle [CURRENT]
  +-- δεδομένα --> [n2] Κανόνες C# [BUILT]
  |   +-- έλεγχοι --> [n3] Απόφαση AGG-01 [BUILT]

[n4] Έγκριση και έκδοση [BUILT]
  +-- εγκρίνει --> [n5] OKF bundle [BUILT]
  |   +-- τεκμήρια --> [n2] Κανόνες C# [BUILT] (αναφορά)

[n6] Rules-as-data [DESIGNED]
  +-- μελλοντικά / στόχος --> [n5] OKF bundle [BUILT] (αναφορά)
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | NCTSite / Oracle | Τρέχοντα δεδομένα | CURRENT (υφιστάμενο) |
| n2 | Κανόνες C# | Τεχνική αξιολόγηση | BUILT (υλοποιημένο) |
| n3 | Απόφαση AGG-01 | Αποτέλεσμα και τεκμήρια | BUILT (υλοποιημένο) |
| n4 | Έγκριση και έκδοση | Διακυβέρνηση γνώσης | BUILT (υλοποιημένο) |
| n5 | OKF bundle | Εγκεκριμένο πακέτο γνώσης | BUILT (υλοποιημένο) |
| n6 | Rules-as-data | Επιλεκτικοί κανόνες ως δεδομένα | DESIGNED (σχεδιασμένο) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | δεδομένα | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n2 -> n3 | έλεγχοι | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n4 -> n5 | εγκρίνει | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n5 -> n2 | τεκμήρια | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n6 -> n5 | μελλοντικά | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |

## Τεκμηρίωση και όρια

- BUILT (υλοποιημένο): C# rule engine (μηχανή κανόνων), επικύρωση/πακετάρισμα OKF και ιχνηλασιμότητα.
- DESIGNED (σχεδιασμένο): επιλεκτική εκτέλεση δηλωτικών επιχειρησιακών κανόνων, με συνθήκες αξιολογήσιμες από μηχανή.
- Αλλαγή σημερινής λογικής απαιτεί κώδικα, δοκιμές και έκδοση εφαρμογής. Hashes (αποτυπώματα) δεν αποδεικνύουν σημασιολογική συμφωνία.
- Απαράβατοι κανόνες ασφαλείας και δικαιώματα παραμένουν σε κώδικα/πολιτική, ανεξάρτητα από γνώση ή LLM.

## Υπόμνημα

- CURRENT (υφιστάμενο)
- BUILT (υλοποιημένο)
- DESIGNED (σχεδιασμένο)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** **C# executes the current AGG-01 checks; OKF organises the approved knowledge that explains
and supports them.** This describes the current implementation, not a requirement that every future
business rule must remain compiled C#.

**Current state: BUILT.** The knowledge compiler validates and packages versioned OKF documents,
vendor constraints, equipment information and source references. It does not generate executable C#
rule bodies. Changes to current executable logic require code review, regression tests and an
application release, not just approval of a revised manual. Source-object references connect results
to supporting knowledge; they are not executable rule compilation.

C# does not require OKF to execute rules. The governed AGG-01 path deliberately requires approved
knowledge as an application control, with these separate responsibilities:

| Concern | Responsible component |
|---|---|
| Current equipment facts | NCTSite/Oracle and their governed interfaces |
| Executable checks and engineering decision | C# rules |
| Requirement, vendor clause and supporting source | OKF objects and references |
| Approved version, integrity and execution pin | Registry and validation code operating on the bundle |

Approval and integrity checks are enforced by application code, not by the format alone. A source ID
or matching hash does not prove that C# agrees with the documented requirement (Q6). The justification
for OKF is governance and traceability, not automatic rule generation or a need to send the full OKF
text to the explanation model. Simpler alternatives are in Q28; implementation evidence is in Appendix B2.

**Target / remaining gap: DESIGNED for general declarative execution.** The rules-as-data plan supports
a selective, controlled declarative layer for frequently changing vendor/business conditions where
justified. This needs machine-evaluable predicates, SME validation, versioning, approval, regression
coverage and compatible release/rollback controls. Prose does not become executable by packaging it
as OKF. Procedural checks can remain C#; safety invariants, authorisation and write restrictions must
remain enforced by code/policy independently of declarative content or LLM output. The plan is not
evidence that all current rules already run as data.

</details>
