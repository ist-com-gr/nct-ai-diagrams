# Q23 - Επαναφορά συμβατού συνόλου

**Πηγή:** [Q23 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** Rollback of application, rules/knowledge, and model configuration

Ασφαλές rollback (επαναφορά) απαιτεί συμβατές εκδόσεις εφαρμογής, κανόνων, γνώσης, προβολών και μοντέλων.

[HTML διάγραμμα](q23-rollback.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] Release manifest [DESIGNED]
  +-- επιλέγει / στόχος --> [n2] Εφαρμογή και C# [DESIGNED]
  |   +-- συμβατότητα / στόχος --> [n3] Γνώση και προβολές [DESIGNED]
  |   |   +-- συμβατότητα / στόχος --> [n4] Router και μοντέλο [CONDITIONAL]
  |   |   |   +-- συντονισμός / στόχος --> [n5] Σχήμα βάσης [CONDITIONAL]
  |   |   |   |   +-- επαλήθευση / στόχος --> [n6] Δοκιμή σε παραγωγική τοπολογία [PENDING]
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | Release manifest | Συμβατό σύνολο εκδόσεων | DESIGNED (σχεδιασμένο) |
| n2 | Εφαρμογή και C# | Προηγούμενη συμβατή εικόνα | DESIGNED (σχεδιασμένο) |
| n3 | Γνώση και προβολές | OKF / vector index / alias | DESIGNED (σχεδιασμένο) |
| n4 | Router και μοντέλο | Παλιά έκδοση αν διατίθεται | CONDITIONAL (υπό όρους) |
| n5 | Σχήμα βάσης | Σχέδιο ανάκτησης μεταβολών | CONDITIONAL (υπό όρους) |
| n6 | Δοκιμή σε παραγωγική τοπολογία | Κοινή επαλήθευση ανάκτησης | PENDING (εκκρεμές) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | επιλέγει | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n2 -> n3 | συμβατότητα | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n3 -> n4 | συμβατότητα | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n4 -> n5 | συντονισμός | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n5 -> n6 | επαλήθευση | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |

## Τεκμηρίωση και όρια

- Το διάγραμμα είναι σύνολο εξαρτήσεων επαναφοράς, όχι απαίτηση σειριακής αλλαγής όλων των στοιχείων.
- Υπάρχει ιστορικό Kubernetes αναθεωρήσεων στην ανάπτυξη, όχι πλήρης παραγωγική πρόβα.
- Alias (δείκτης έκδοσης) γνώσης δεν επαναφέρει C#. Αλλαγή και των δύο απαιτεί ελεγμένο ζεύγος γνώσης/εφαρμογής.
- Μεταβολές βάσης αναστρέφονται υπό όρους. Απόσυρση μοντέλου από πάροχο περιορίζει πραγματικά τη δυνατότητα επαναφοράς.

## Υπόμνημα

- DESIGNED (σχεδιασμένο)
- CONDITIONAL (υπό όρους)
- PENDING (εκκρεμές)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** Rollback is designed as a first-class capability, independently for: application containers
(previous signed image/Helm release), agent definitions (versioned manifest), prompt templates
(versioned config), policy (versioned package, subject to compatibility), executable C# rules (the
compatible application image today; independent declarative rule bundles remain a target), OKF
knowledge bundles (immutable bundle plus alias), the vector index (alias switch), model
routing configuration (previous routing manifest), and model deployment (route to the previous
deployment where the provider version is still available); database schema migrations are rolled back
conditionally, following an expand/contract pattern with no destructive migration shipped in the same
release without a recovery plan. Independent rollback is only safe within a stated compatibility
matrix carried in the release manifest.

**Current state.** Development Kubernetes revision history exists (Appendix B4), but does not prove
a successful rollback across all dependent artifacts.

**Target / remaining gap.** Rehearse rollback against the deployed Production topology, proving image,
application configuration and compatible knowledge/model configuration recovery together.
A knowledge alias alone does
not roll back executable C# rule logic.

If a business-rule change spans OKF and C#, the release and rollback targets must retain a reviewed,
compatible pair of knowledge and application versions. Restoring only one side can recreate the
semantic drift described in Q6 even when all artifact hashes are valid.

</details>
