# Q27 - Αναβαθμίσεις πίσω από σταθερή σύμβαση

**Πηγή:** [Q27 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** Protecting curated views, MCP interfaces and deterministic rules from NCTSite/Oracle upgrades

Οι εσωτερικές αλλαγές NCTSite απομονώνονται με επιμελημένες όψεις/APIs, όχι με άμεση εξάρτηση από αυθαίρετους πίνακες.

[HTML διάγραμμα](q27-upgrade-contracts.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] NCTSite εσωτερικό σχήμα [CURRENT]
  +-- απομόνωση --> [n2] Σταθερές όψεις / APIs [PARTIAL]
  |   +-- σύμβαση --> [n3] MCP και κανόνες C# [BUILT]
  |   |   +-- συμβατότητα / στόχος --> [n4] Δοκιμές υποψήφιας αλλαγής [REQUIRED]
  |   |   |   +-- αποδοχή / στόχος --> [n5] UAT και Golden Set [REQUIRED]
  |   |   |   |   +-- έγκριση / στόχος --> [n6] Ελεγχόμενη αναβάθμιση [DESIGNED]
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | NCTSite εσωτερικό σχήμα | Φυσικοί πίνακες | CURRENT (υφιστάμενο) |
| n2 | Σταθερές όψεις / APIs | Έκδοση σύμβασης δεδομένων | PARTIAL (μερικώς υλοποιημένο) |
| n3 | MCP και κανόνες C# | Συμφωνημένα πεδία / σημασία | BUILT (υλοποιημένο) |
| n4 | Δοκιμές υποψήφιας αλλαγής | Τύποι / μονάδες / null / freshness | REQUIRED (απαίτηση) |
| n5 | UAT και Golden Set | Μη παλινδρόμηση / απόδοση | REQUIRED (απαίτηση) |
| n6 | Ελεγχόμενη αναβάθμιση | Αυτοματοποιημένη πύλη εκκρεμεί | DESIGNED (σχεδιασμένο) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | απομόνωση | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n2 -> n3 | σύμβαση | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n3 -> n4 | συμβατότητα | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n4 -> n5 | αποδοχή | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n5 -> n6 | έγκριση | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |

## Τεκμηρίωση και όρια

- Οι επιμελημένες όψεις έχουν χρησιμοποιηθεί ζωντανά στην ανάπτυξη. Η σημερινή επανεκτέλεση δοκιμών σύμβασης είναι χειροκίνητη.
- DESIGNED (σχεδιασμένη): αυτόματη pre-upgrade regression (δοκιμή μη παλινδρόμησης πριν αναβάθμιση) στο υποψήφιο σχήμα.
- Ίδια ονόματα/τύποι μπορεί να κρύβουν διαφορετικές μονάδες, null semantics (σημασία κενών τιμών), φίλτρα ή παλαιότητα.
- Αν δεν διατηρείται η σύμβαση, απαιτείται νέα έκδοση και συντονισμένη αλλαγή API/MCP/κανόνων, όχι υπόθεση πλήρους διαφάνειας.

## Υπόμνημα

- CURRENT (υφιστάμενο)
- PARTIAL (μερικώς υλοποιημένο)
- BUILT (υλοποιημένο)
- REQUIRED (απαίτηση)
- DESIGNED (σχεδιασμένο)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** The key is a stable anti-corruption/contract layer: NCTSite's internal tables feed curated
views/APIs, which feed the NCT-AI contract, which feeds MCP tools and deterministic rules — NCT-AI
never depends directly on arbitrary internal physical table layouts. Controls: versioned curated views,
an explicit interface contract, schema-compatibility tests, an Oracle contract test suite, test-data
fixtures, pre-upgrade UAT regression, a dependency manifest, backward-compatible view changes, and
change notification from the NCTSite/DB owners. Before an upgrade: object-existence tests, column-type
tests, sample-query tests, semantic data tests, the agent Golden Set, and performance tests all run; if
NCTSite must change a physical schema, the existing curated contract is maintained so NCT-AI continues
unchanged, and a new contract version is introduced only for an intentional breaking semantic change.
**DESIGNED**, matching this pattern; the curated views themselves exist and have been queried live
(Q16), which is stronger evidence than a design intention — what remains is an automated pre-upgrade
regression suite running the Oracle contract tests against a candidate schema before an upgrade ships;
today that is a manual re-run of the existing contract test suite. Passing the existing Golden Set
does not prove every schema change is transparent: unchanged field names/types can hide changed
units, null semantics, filtering or freshness. Extend the contract and regression cases for the actual
upgrade, and coordinate API/MCP/rule changes when the stable contract cannot be preserved.

</details>
