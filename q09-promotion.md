# Q09 - Ελεγχόμενη προώθηση παραδοτέων

**Πηγή:** [Q9 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** Promotion of model versions, rules, knowledge and embeddings between UAT/Production, and "learning" transfer

Η διαδρομή στόχου προωθεί το ίδιο εγκεκριμένο παραδοτέο από DEV σε UAT και παραγωγή, χωρίς αυτόματη μάθηση.

[HTML διάγραμμα](q09-promotion.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] DEV κατασκευή [PARTIAL]
  +-- δέσμευση / στόχος --> [n2] Αμετάβλητη υποψήφια [DESIGNED]
  |   +-- δοκιμές / στόχος --> [n3] UAT αποδοχή [DESIGNED]
  |   |   +-- έγκριση / στόχος --> [n4] Release manifest [DESIGNED]
  |   |   |   +-- προώθηση / στόχος --> [n5] Παραγωγή [DESIGNED]
  |   |   |   |   +-- παρατηρήσεις / στόχος --> [n6] Ανατροφοδότηση [DESIGNED]
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | DEV κατασκευή | Εφαρμογή / κανόνες / γνώση | PARTIAL (μερικώς υλοποιημένο) |
| n2 | Αμετάβλητη υποψήφια | Εκδόσεις και αποτυπώματα | DESIGNED (σχεδιασμένο) |
| n3 | UAT αποδοχή | Δοκιμές / ασφάλεια / SME | DESIGNED (σχεδιασμένο) |
| n4 | Release manifest | Μητρώο εγκεκριμένης έκδοσης | DESIGNED (σχεδιασμένο) |
| n5 | Παραγωγή | Ίδια εγκεκριμένα παραδοτέα | DESIGNED (σχεδιασμένο) |
| n6 | Ανατροφοδότηση | Νέα υποψήφια, όχι αυτομάθηση | DESIGNED (σχεδιασμένο) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | δέσμευση | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n2 -> n3 | δοκιμές | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n3 -> n4 | έγκριση | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n4 -> n5 | προώθηση | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n5 -> n6 | παρατηρήσεις | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |

## Τεκμηρίωση και όρια

- BUILT (υλοποιημένο): ενεργοποίηση εγκεκριμένης γνώσης και pinning (δέσμευση έκδοσης) στην ανάπτυξη.
- End-to-end promotion (ολοκληρωμένη προώθηση) στην παραγωγή παραμένει DESIGNED (σχεδιασμένη).
- Κανόνες C# ακολουθούν την εφαρμογή. Embeddings (διανυσματικές αναπαραστάσεις) απαιτούν ίδιο source bundle, chunking, μοντέλο και manifest (πηγή, τμηματοποίηση, μητρώο).
- Ίδιο hash πηγής δεν σημαίνει ίδια bytes embeddings. Η πολιτική αναβάθμισης παρόχου ελέγχεται χωριστά.

## Υπόμνημα

- PARTIAL (μερικώς υλοποιημένο)
- DESIGNED (σχεδιασμένο)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** The target promotion process is artifact-based and immutable, across application images,
agent definitions, prompt templates, policy versions, executable rule code, approved OKF bundles,
embedding-model configuration, vector-index manifests and model-routing configuration: build once,
promote the same approved artifact
through DEV → candidate → UAT (regression, security tests, SME acceptance) → an approved release
manifest → Production, never rebuilding a "similar" bundle from source once UAT has validated it.
Embeddings may be physically regenerated per environment but must derive from the same approved
source bundle, chunking version, embedding-model version and projection manifest, ideally built
side-by-side and switched via an alias after validation. Model changes are configuration-controlled,
but provider-side upgrade policy must be governed separately (Q21; Appendix B4). There is no direct,
automatic "Production learns and updates itself" path — Production telemetry/feedback can inform a
UAT/DEV candidate, but only through the governed regression-and-approval lifecycle in Q7–Q8, in one
direction, human-gated at release approval. **BUILT** for knowledge authority activation and pinning in
development (Q5); this is not proof of cross-environment promotion. End-to-end Production promotion,
including model configuration and executable rule/application releases, remains **DESIGNED** and
unverified. Current C# rules are promoted with the application, not by an independent OKF alias.

An equal source-bundle hash does not by itself prove equal embedding bytes. Re-projection also depends
on preprocessing, chunking, model/version and service behaviour. Record the projection manifest and
validate retrieval quality; retain exact artifacts where byte-for-byte reproducibility is required.

</details>
