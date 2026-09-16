# Q05 - Από εγχειρίδιο σε έκδοση γνώσης

**Πηγή:** [Q5 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** New/revised vendor manual → approved Production knowledge/OKF

Η προτεινόμενη διαδρομή έγκρισης διαχωρίζει προτάσεις γνώσης από εξουσιοδοτημένη ενεργοποίηση.

[HTML διάγραμμα](q05-knowledge-release.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] Πηγή και αναθεώρηση [MANUAL]
  +-- πρόταση / στόχος --> [n2] Υποψήφια γνώση [ASSISTED]
  |   +-- επικύρωση --> [n3] OKF και δοκιμές [BUILT]
  |   |   +-- έλεγχος --> [n4] Επιθεώρηση SME [MANUAL]
  |   |   |   +-- έγκριση / στόχος --> [n5] UAT και ενεργοποίηση [DESIGNED]
  |   |   |   |   +-- ίδια έκδοση / στόχος --> [n6] Εκτέλεση με pin [DEV]
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | Πηγή και αναθεώρηση | Αμετάβλητο αρχείο | MANUAL (χειροκίνητο) |
| n2 | Υποψήφια γνώση | Εξαγωγή / ερμηνεία | ASSISTED (υποβοηθούμενο) |
| n3 | OKF και δοκιμές | Πακέτο / Golden Set | BUILT (υλοποιημένο) |
| n4 | Επιθεώρηση SME | Ανθρώπινη έγκριση | MANUAL (χειροκίνητο) |
| n5 | UAT και ενεργοποίηση | Προώθηση μεταξύ περιβαλλόντων | DESIGNED (σχεδιασμένο) |
| n6 | Εκτέλεση με pin | Δέσμευση εγκεκριμένης έκδοσης | DEV (ανάπτυξη) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | πρόταση | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n2 -> n3 | επικύρωση | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n3 -> n4 | έλεγχος | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n4 -> n5 | έγκριση | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n5 -> n6 | ίδια έκδοση | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |

## Τεκμηρίωση και όρια

- Η συνολική αυτοματοποιημένη ροή PDF-to-production (από αρχείο σε παραγωγή) δεν έχει αποδειχθεί.
- Υπάρχει ενεργή εγκεκριμένη έκδοση στην ανάπτυξη. Η επιθεώρηση SME (ειδικού πεδίου) παραμένει χειροκίνητη.
- Αλλαγή λογικής κανόνα απαιτεί και χωριστή έκδοση C#, όχι μόνο νέο OKF.
- 1-3 εργάσιμες ημέρες ή μία εβδομάδα και άνω είναι ενδεικτικές εκτιμήσεις, όχι SLA (συμφωνία επιπέδου υπηρεσίας).

## Υπόμνημα

- MANUAL (χειροκίνητο)
- ASSISTED (υποβοηθούμενο)
- BUILT (υλοποιημένο)
- DESIGNED (σχεδιασμένο)
- DEV (ανάπτυξη)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** The target lifecycle is versioned: register the source and revision, retain an immutable
source artifact, extract and normalise content, propose knowledge changes, analyse their impact, build
a candidate OKF bundle, validate schema and references, build retrieval projections, run Golden Set
regression, obtain SME review and an approval record, validate in UAT, then promote to Production.
AI-assisted extraction produces proposals, never approvals. Production policy requires approved
sources before activation in the trusted knowledge set.

**Partially BUILT:** OKF authoring and knowledge compilation, AGG-01 Golden Set regression, and the
authority-release mechanism that binds an execution to an approved knowledge version. An Active release
is recorded in the development database (`CONFIG.md`, "PoTP authoritative knowledge pinning").
SME review remains manual and out of band; this is not evidence of a completed automated
DEV-to-UAT-to-Production release.

Knowledge compilation here means validated knowledge packaging and projections, not generation of
executable C# rules. When a manual revision changes implemented rule logic, a separate code change,
regression suite and approved application release are required alongside the knowledge release (Q4).

The additional response proposes separate Knowledge Author and Policy Reviewer responsibilities;
this separation of duties should be an acceptance requirement, not inferred from role names alone.
Its XDOM-01 OCR/manual-ingestion sequence is a target integration, not evidence of a completed
PDF-to-production pipeline. The suggested 1–3 working days for a familiar equipment family, or a week
or more for a new structure, is an indicative estimate requiring SME availability, extraction quality,
test coverage and any necessary C# change to be assessed. It is not a delivery SLA.

</details>
