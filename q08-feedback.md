# Q08 - Ανατροφοδότηση και νέες δοκιμές

**Πηγή:** [Q8 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** Rejection clustering, Golden Set expansion, and mandatory SME approval

Η δομημένη ανατροφοδότηση είναι απαίτηση σχεδιασμού· μεμονωμένα ίχνη απόρριψης δεν συνιστούν ολοκληρωμένο σύστημα βελτίωσης.

[HTML διάγραμμα](q08-feedback.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] Απόρριψη μηχανικού [INPUT]
  +-- καταγραφή / στόχος --> [n2] Δομημένο feedback [DESIGNED]
  |   +-- ανάλυση / στόχος --> [n3] Clustering [DESIGNED]
  |   |   +-- διερεύνηση / στόχος --> [n4] Αναπαραγωγή σφάλματος [REQUIRED]
  |   |   |   +-- νέα δοκιμή / στόχος --> [n5] Golden Set [BUILT]
  |   |   |   |   +-- αποδοχή / στόχος --> [n6] Εγκεκριμένη διόρθωση [REQUIRED]
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | Απόρριψη μηχανικού | Πηγή προβλήματος | INPUT (είσοδος) |
| n2 | Δομημένο feedback | Συμβάν ανατροφοδότησης | DESIGNED (σχεδιασμένο) |
| n3 | Clustering | Ομαδοποίηση απορρίψεων | DESIGNED (σχεδιασμένο) |
| n4 | Αναπαραγωγή σφάλματος | Σωστό αποτέλεσμα SME | REQUIRED (απαίτηση) |
| n5 | Golden Set | Δοκιμές μη παλινδρόμησης | BUILT (υλοποιημένο) |
| n6 | Εγκεκριμένη διόρθωση | Κώδικας / γνώση / prompt | REQUIRED (απαίτηση) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | καταγραφή | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n2 -> n3 | ανάλυση | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n3 -> n4 | διερεύνηση | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n4 -> n5 | νέα δοκιμή | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n5 -> n6 | αποδοχή | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |

## Τεκμηρίωση και όρια

- Golden Set (σύνολο αναφοράς): BUILT (υλοποιημένο) για AGG-01.
- Η πλήρης μόνιμη καταγραφή και η ομαδοποίηση δεν τεκμηριώνονται ως λειτουργική ενιαία διαδρομή.
- Ανάλυση και υποψήφια πρόταση δεν εξουσιοδοτούν παραγωγική ενεργοποίηση.
- Αλλαγές αποτελέσματος χρειάζονται SME/Knowledge Approver (ειδικό/εγκριτή γνώσης) και χωριστούς ελέγχους έκδοσης C#.

## Υπόμνημα

- INPUT (είσοδος)
- DESIGNED (σχεδιασμένο)
- REQUIRED (απαίτηση)
- BUILT (υλοποιημένο)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** Engineer feedback should be captured as a structured feedback event, linked to the
recommendation, evidence and relevant versions. Rejection clustering would then group similar failures
to identify systemic issues. This is the intended improvement process, not a claim that a persistent,
end-to-end feedback capture and analytics pipeline is operational.

**Current state.** Golden Set regression is **BUILT** for AGG-01. Individual approval/rejection audit
records do not establish a general feedback entity/API or a complete feedback loop. End-to-end
feedback tooling and rejection clustering remain **DESIGNED** (Q7; Appendix B2).

**Target / remaining gap.** A confirmed failure should become a reproducible regression case:
reproduce, determine the correct expected outcome, extend the Golden Set, fix the rule/knowledge/prompt
and prove that the regression passes. Feedback is triage evidence, never a direct conversion into
Production rules. Analytics and draft candidates do not themselves authorise Production activation.
Any change to Production knowledge or rules that can alter an engineering outcome requires
SME/Knowledge Approver approval before activation. Q5 governs knowledge releases; executable C#
changes also require the separate code-review, regression and application-release controls in Q4/Q12.

</details>
