# Q06 - Επίδραση αλλαγής και σημασιολογική απόκλιση

**Πηγή:** [Q6 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** Automated / AI-assisted / manual steps, and impact detection

Οι σύνδεσμοι πηγής, κανόνα και δοκιμής στηρίζουν έλεγχο συνέπειας, χωρίς να αποδεικνύουν αυτόματα ισοδυναμία νοήματος.

[HTML διάγραμμα](q06-impact-and-drift.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] Ρήτρα πηγής [MANUAL]
  +-- τεκμηρίωση --> [n2] Αντικείμενο OKF [BUILT]
  |   +-- αντιστοίχιση --> [n3] Κανόνας C# [BUILT]
  |   |   +-- ελέγχεται --> [n4] Δοκιμή αποδοχής [REQUIRED]
  |   |   |   +-- αποδοχή / στόχος --> [n5] Συντονισμένη έκδοση [REQUIRED]

[n6] Impact Set [DESIGNED]
  +-- αποτίμηση / στόχος --> [n5] Συντονισμένη έκδοση [REQUIRED] (αναφορά)
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | Ρήτρα πηγής | Απαίτηση προμηθευτή | MANUAL (χειροκίνητο) |
| n2 | Αντικείμενο OKF | Παραπομπή και έκδοση | BUILT (υλοποιημένο) |
| n3 | Κανόνας C# | Εκτελέσιμη ερμηνεία | BUILT (υλοποιημένο) |
| n4 | Δοκιμή αποδοχής | Αναμενόμενο αποτέλεσμα SME | REQUIRED (απαίτηση) |
| n5 | Συντονισμένη έκδοση | Γνώση και εφαρμογή μαζί | REQUIRED (απαίτηση) |
| n6 | Impact Set | Πλήρες σύνολο επιπτώσεων | DESIGNED (σχεδιασμένο) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | τεκμηρίωση | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n2 -> n3 | αντιστοίχιση | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n3 -> n4 | ελέγχεται | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n4 -> n5 | αποδοχή | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n6 -> n5 | αποτίμηση | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |

## Τεκμηρίωση και όρια

- BUILT (υλοποιημένο): εργαλεία επικύρωσης OKF και Golden Set (σύνολο αναφοράς), όχι πλήρης αυτόματη ανάλυση επιπτώσεων.
- Semantic drift (σημασιολογική απόκλιση): αλλάζει η τεκμηρίωση χωρίς τον κανόνα ή ο κανόνας χωρίς τη γνώση.
- Προτεινόμενοι έλεγχοι: σαφής αντιστοίχιση, αναμενόμενα αποτελέσματα SME και διάκριση αλλαγής κειμένου από αλλαγή συμπεριφοράς.
- Οι προτάσεις LLM δεν υποκαθιστούν έγκριση και δεν είναι δυνατότητα της σημερινής κλήσης εξήγησης AGG-01.

## Υπόμνημα

- MANUAL (χειροκίνητο)
- BUILT (υλοποιημένο)
- REQUIRED (απαίτηση)
- DESIGNED (σχεδιασμένο)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** The automation boundary must distinguish available tooling from the complete target flow.

| Step | Current position |
|---|---|
| OKF validation/packaging and AGG-01 Golden Set regression | **BUILT** tooling; see Q4–Q5 |
| Source registration, engineering interpretation, SME approval and release authorisation | Manual governance responsibilities |
| Extraction of proposed knowledge, semantic comparison, contradiction analysis and proposed test cases | AI-assisted authoring activities, not an autonomous approved ingestion service |
| Complete source-revision detection, impact analysis, projection rebuild and cross-environment promotion | **DESIGNED** as an end-to-end automated flow; individual components do not prove the whole flow |
| Executable rule changes | C# implementation and release today; general declarative rule compilation remains **DESIGNED** |
| Artifact signing and signature verification in a release | Mechanisms exist; successful release execution is not yet evidenced (Q12, Q26) |

The proposed lineage graph links source clause, OKF object, executable rule, agent, bundle, Golden test
case and retrieval projection in both directions. Automatically deriving a complete Impact Set from a
manual revision remains **DESIGNED**. Today an author uses bundle cross-references and code/test
dependencies to assess the impact. An AI-produced proposal never replaces engineering approval.

The main consistency risk is semantic drift: an OKF requirement can change without the C# rule
changing, or vice versa. Proposed controls are an explicit source-clause → OKF object → C# rule →
test mapping; SME-approved expected outcomes in shared acceptance tests; classification of each
revision as documentation-only or behaviour-changing; and coordinated knowledge/code review and
release for the latter. IDs, hashes and graph links support that review but do not establish semantic
equivalence. Full automatic knowledge-to-code equivalence checking is not claimed.

LLM-assisted comparison of manuals, extraction of candidate requirements and identification of
ambiguities are useful development directions. Each needs a separately implemented and evaluated
scenario path; none is an implicit capability of today's AGG-01 explanation call.

</details>
