# Q21 - Έλεγχος αλλαγής μοντέλου

**Πηγή:** [Q21 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** Validation/regression before changing the LLM or embedding model in Production

Νέο μοντέλο σημαίνει ελεγχόμενη αλλαγή εξάρτησης, με δοκιμές ποιότητας/ασφάλειας και σύγκριση με λύση χωρίς LLM.

[HTML διάγραμμα](q21-model-validation.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] Υποψήφιο μοντέλο [DESIGNED]
  +-- δοκιμές / στόχος --> [n2] Golden Set και ασφάλεια [REQUIRED]
  |   +-- μετρήσεις / στόχος --> [n3] Σύγκριση λύσεων [REQUIRED]
  |   |   +-- επικύρωση / στόχος --> [n4] UAT / shadow / canary [DESIGNED]
  |   |   |   +-- αποδοχή / στόχος --> [n5] Έγκριση και αλλαγή [DESIGNED]
  |   |   |   |   +-- έλεγχος / στόχος --> [n6] Παρακολούθηση / rollback [DESIGNED]
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | Υποψήφιο μοντέλο | Έκδοση και δυνατότητες | DESIGNED (σχεδιασμένο) |
| n2 | Golden Set και ασφάλεια | Αναμενόμενα αποτελέσματα SME | REQUIRED (απαίτηση) |
| n3 | Σύγκριση λύσεων | Ποιότητα / latency / κόστος | REQUIRED (απαίτηση) |
| n4 | UAT / shadow / canary | Δοκιμή χωρίς άμεση αντικατάσταση | DESIGNED (σχεδιασμένο) |
| n5 | Έγκριση και αλλαγή | Ρύθμιση Router / index alias | DESIGNED (σχεδιασμένο) |
| n6 | Παρακολούθηση / rollback | Μόνο όσο υπάρχει παλιά έκδοση | DESIGNED (σχεδιασμένο) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | δοκιμές | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n2 -> n3 | μετρήσεις | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n3 -> n4 | επικύρωση | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n4 -> n5 | αποδοχή | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n5 -> n6 | έλεγχος | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |

## Τεκμηρίωση και όρια

- DESIGNED (σχεδιασμένη) συνολική διαδικασία· αλλαγές μοντέλου έχουν δοκιμαστεί στην ανάπτυξη, όχι πλήρης παραγωγική σύγκριση πριν/μετά.
- Με σταθερά δεδομένα, γνώση και C#, αλλαγή μοντέλου εξήγησης δεν πρέπει να αλλάζει απόφαση AGG-01.
- Embeddings (διανυσματικές αναπαραστάσεις): ξεχωριστός νέος δείκτης, έλεγχος ανάκτησης, αλλαγή alias (δείκτη έκδοσης), ποτέ ανάμειξη ασύμβατων χώρων.
- Ο verifier (ελεγκτής μοντέλου) δεν είναι ground truth (αλήθεια αναφοράς). Απόσυρση μοντέλου μπορεί να καταστήσει αδύνατο το rollback (επαναφορά).

## Υπόμνημα

- DESIGNED (σχεδιασμένο)
- REQUIRED (απαίτηση)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** A model change is a controlled software-dependency change: register the candidate
model/version → deterministic compatibility tests → offline Golden Set → quality comparison against
the current production model → evidence correctness, tool-call correctness, policy/safety failures,
latency and token/cost profile measured → adversarial/prompt-injection tests → UAT deployment →
optional shadow/canary → acceptance → promote the model-routing configuration → monitor early
Production traffic → retain a rollback configuration. Embedding-model changes carry additional
controls — never mixing incompatible vector spaces in one index — building a new side-by-side vector
index, evaluating retrieval recall/precision against the old, running through UAT, and switching via an
atomic alias with the old index retained through the rollback window. **Current state.** Development model configuration changes have been exercised, but a full
Golden-Set-based before/after Production model-change comparison has not. The complete release
process remains **DESIGNED**; details are in Appendix B4.

**Target / remaining gap.** Govern actual provider versions and upgrade policies, monitor retirement
notices and validate replacements before retirement. Rollback requires the previous model to remain
available at the provider; retaining a routing configuration is not enough.

The additional response's AGG-01 decision-invariance check is useful: hold facts, approved knowledge
and executable rules fixed, then verify that changing an optional explanation model cannot alter the
decision. Record the actual Golden Set version, case count and results rather than treating the
source's "12 scenarios" as a permanent coverage guarantee. Overall acceptance rate alone is not enough:
release gates must separately cover safety, evidence fidelity and per-scenario regressions.

Also compare each proposed model use against a non-LLM baseline. For AGG-01 wording, compare with
fixed templates using answer correctness/clarity, engineer task time, correction rate, response time
and cost. For general chat, test grounding and permitted tool selection; for `Verify`, test false
rejections, missed defects and unavailable/malformed verdicts. A model verifier is an additional check,
not the source of ground truth for its own evaluation. Expert-approved expected outcomes remain needed.

</details>
