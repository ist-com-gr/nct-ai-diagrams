# Q22 - Εγκεκριμένη έκδοση και παλαιότητα

**Πηγή:** [Q22 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** Detecting obsolete rules/knowledge and preventing stale-knowledge recommendations

Το pinning (δέσμευση έκδοσης) εξασφαλίζει ποια γνώση χρησιμοποιήθηκε, όχι ότι δεν έχει λήξει ή ανακληθεί.

[HTML διάγραμμα](q22-knowledge-currency.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] Νέα εκτέλεση [BUILT]
  +-- επικύρωση --> [n2] Έλεγχος γνώσης [BUILT]
  |   +-- δέσμευση --> [n3] Pin έκδοσης [BUILT]
  |   |   +-- επανάληψη --> [n4] Temporal retry [BUILT]

[n6] Απόσυρση προβολών [DESIGNED]
  +-- χωριστή πολιτική / στόχος --> [n5] Πολιτική παλαιότητας [DESIGNED]
  |   +-- επιπλέον όριο / στόχος --> [n2] Έλεγχος γνώσης [BUILT] (αναφορά)
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | Νέα εκτέλεση | Επίλυση Active release | BUILT (υλοποιημένο) |
| n2 | Έλεγχος γνώσης | Έγκριση / ανάκληση / hash | BUILT (υλοποιημένο) |
| n3 | Pin έκδοσης | Ακριβής δέσμευση γνώσης | BUILT (υλοποιημένο) |
| n4 | Temporal retry | Κρατά το ίδιο pin | BUILT (υλοποιημένο) |
| n5 | Πολιτική παλαιότητας | Ημερομηνίες / συμβατότητα | DESIGNED (σχεδιασμένο) |
| n6 | Απόσυρση προβολών | Δείκτες / γράφος / ιστορικό | DESIGNED (σχεδιασμένο) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | επικύρωση | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n2 -> n3 | δέσμευση | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n3 -> n4 | επανάληψη | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n5 -> n2 | επιπλέον όριο | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n6 -> n5 | χωριστή πολιτική | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |

## Τεκμηρίωση και όρια

- BUILT (υλοποιημένα): έγκριση, υπερκάλυψη, καταργημένο περιεχόμενο, αποτυπώματα και ετοιμότητα προβολών.
- DESIGNED (σχεδιασμένα): καθολικές ημερομηνίες ισχύος/επιθεώρησης και αυτόματη ανίχνευση παλαιών πηγών.
- Το αρχικό pin δεν είναι γενική άδεια χρήσης περιεχομένου που απαγορεύει πλέον η πολιτική.
- Ενεργοποίηση νέας έκδοσης δεν αποδεικνύει φυσική διαγραφή παλαιών διανυσμάτων ή κόμβων γράφου.

## Υπόμνημα

- BUILT (υλοποιημένο)
- DESIGNED (σχεδιασμένο)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** **BUILT:** approved-release resolution, immutable version pinning, integrity checks and
explicit lifecycle controls. OKF objects have identifiers, governance/approval metadata, version and
hash information. The PoTP authority validator checks approval, supersession, deprecated content,
hashes and projection readiness. These controls do not establish that every object has an effective
date, review deadline or equipment/software compatibility constraint, nor that those fields are
automatically enforced.

A new governed execution resolves the Active authority release and pins its exact version. Temporal
retries retain the original pin instead of silently switching to a newly Active release; the resolver
also supports lookup of that exact pinned version. Explicit revocation/supersession and validation
remain separate controls: a recorded pin is not blanket permission to use content rejected by policy.
Audit inspection of historical evidence is distinct from authorising a new recommendation.
Implementation evidence is in Appendix B2.

**DESIGNED:** mandatory effective-from/to and review-date metadata, version-compatibility enforcement,
automatic detection of obsolete source revisions, and a date-based staleness gate that refuses or
escalates expired knowledge. Activation of a new release does not by itself prove physical deletion
or tombstoning of old vector chunks/graph nodes. Projection retirement, historical retention and the
handling of in-flight workflows need explicit policy and tests. Until then, owners must review
currency and explicitly withdraw or supersede outdated knowledge.

</details>
