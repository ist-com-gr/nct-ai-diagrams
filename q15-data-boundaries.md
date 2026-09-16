# Q15 - Περιεχόμενο, διατήρηση και γεωγραφία

**Πηγή:** [Q15 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** Data processed by NCT-AI, what reaches Azure OpenAI, and minimisation/retention/deletion policy

Η αποθήκευση NCT-AI, η διατήρηση παρόχου και η γεωγραφία επεξεργασίας είναι διαφορετικά όρια.

[HTML διάγραμμα](q15-data-boundaries.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] Περιεχόμενο ανά ρόλο [MIXED]
  +-- επιλογή --> [n2] Ελαχιστοποίηση [REQUIRED]
  |   +-- επεξεργασία --> [n3] Πάροχος μοντέλου [CONFIG]
  |   |   +-- χωριστό όριο --> [n4] Διατήρηση παρόχου [VERIFY]
  |   +-- καταγραφή --> [n5] Αποθήκευση NCT-AI [CONFIG]
  |   |   +-- διατήρηση / στόχος --> [n6] Διαγραφή και πολιτική [DESIGNED]
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | Περιεχόμενο ανά ρόλο | Chat / embeddings / Verify | MIXED (μικτή κατάσταση ανά συνιστώσα) |
| n2 | Ελαχιστοποίηση | Επιλεγμένα πεδία και τεκμήρια | REQUIRED (απαίτηση) |
| n3 | Πάροχος μοντέλου | Chat EU / embeddings Global | CONFIG (ρύθμιση αποθετηρίου) |
| n4 | Διατήρηση παρόχου | Χωριστή έγκριση εξαίρεσης | VERIFY (προς επαλήθευση) |
| n5 | Αποθήκευση NCT-AI | CaptureContent ανά περιβάλλον | CONFIG (ρύθμιση αποθετηρίου) |
| n6 | Διαγραφή και πολιτική | Αρχεία / προβολές / αντίγραφα | DESIGNED (σχεδιασμένο) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | επιλογή | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n2 -> n3 | επεξεργασία | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n3 -> n4 | χωριστό όριο | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n2 -> n5 | καταγραφή | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n5 -> n6 | διατήρηση | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |

## Τεκμηρίωση και όρια

- CONFIG (ρύθμιση αποθετηρίου), όχι ζωντανός έλεγχος: chat DataZoneStandard, embeddings GlobalStandard. Το Global δεν εγγυάται επεξεργασία μόνο εντός ΕΕ.
- Η εξήγηση AGG-01 στέλνει αποτέλεσμα/λόγο/id, όχι όλη την Oracle ή το OKF. Το Verify λαμβάνει επιλεγμένα προηγούμενα παραδοτέα.
- CaptureContent=None είναι προεπιλογή βιβλιοθήκης, ενώ dev/local χρησιμοποιεί BoundedMetadata. Τοπική καταγραφή δεν ελέγχει διατήρηση παρόχου.
- Οι περίοδοι διατήρησης και το πενταετές audit (καταγραφή ελέγχου) είναι προτάσεις προς συμφωνία, όχι ενεργές εγγυήσεις ή επαληθευμένη νομική υποχρέωση.

## Υπόμνημα

- MIXED (μικτή κατάσταση ανά συνιστώσα)
- REQUIRED (απαίτηση)
- CONFIG (ρύθμιση αποθετηρίου)
- VERIFY (προς επαλήθευση)
- DESIGNED (σχεδιασμένο)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** NCT-AI processes user requests, identity/session metadata, selected engineering facts,
approved knowledge and retrieval projections, workflow state, model requests/responses, and audit
references. Oracle remains authoritative for the operational facts. The intended model payload is a
user question plus selected knowledge snippets, curated facts and rule outcomes, not complete Oracle
tables, credentials or unrelated user/history data. Field allowlists, typed tools and context budgets
support minimisation; classification and masking coverage must be validated per use case before
Production.

Payloads differ by path: model-backed chat can send the user prompt, selected history and retrieved
context; embedding projection sends selected knowledge chunks when that projection is generated or
rebuilt, not necessarily only once. The PoTP explanation path sends decision/reason information and an
element identifier, not the full Oracle facts or full OKF bundle. Its deterministic decision needs no
model call. The new response's free-text routing and approval-reply classification examples must not be
treated as universal implemented model calls; the current intent router is rule-based (Q10).

The plan verifier receives selected prior-step artifacts, so those artifacts need their own
classification and minimisation review. Its lack of tools does not mean no content is sent to the
model. Conversely, the AGG-01 explanation payload does not justify sending a complete OKF corpus.

Three separate boundaries govern retention and processing:

| Boundary | Current position and requirement |
|---|---|
| NCT-AI storage | The ledger library defaults to `CaptureContent=None`, but `dev/local/start.sh` explicitly defaults to `BoundedMetadata`. Effective configuration must be checked per environment. Outside Development, enabling that capture requires a bounded content-retention setting; this startup validation does not prove deletion across all stores. |
| Provider retention | Microsoft may retain selected prompts/completions for abuse-monitoring human review. Modified monitoring requires separate approval; none is assumed here. Local capture settings do not control it. |
| Processing geography | Repository Bicep defaults specify chat `gpt-5.4-mini` as `DataZoneStandard` and embeddings `text-embedding-3-small` as `GlobalStandard`. This is configuration evidence, not a fresh inspection of live deployments. |

Microsoft states that customer content is not used to train foundation models without permission.
EU DataZone processing stays within the EU; Global processing can occur elsewhere. Storage geography
is distinct, and a private endpoint does not determine inference location.
[Microsoft data privacy and processing geography](https://learn.microsoft.com/en-us/azure/foundry/responsible-ai/openai/data-privacy).

For an EU-only processing requirement, the embeddings deployment must be changed to a supported
compliant option or receive explicit approval for the exception. Verify each live deployment's SKU,
model/version, retention features and abuse-monitoring status before approval.

Proposed **NCT-AI** retention periods, subject to Telekom approval: conversations 30 days UAT /
90 days Production; diagnostic prompt/completion copies 7–30 days UAT / 30 days or disabled in
Production; feedback 12–24 months, de-identified where possible; embeddings until source retirement;
audit/security metadata 90–180 days UAT / 12–24+ months Production. These are policy proposals, not
implemented retention guarantees or provider retention periods. Deletion procedures must cover
conversations, derived projections, workflow history and backups according to the approved policy,
with verification of expiry and deletion. Q25 and Q29 distinguish usage measurements from cost estimates.

The additional response proposes 30-day prompts, 90-day active conversations followed by archive,
two-year invocation metadata and at least five-year audit retention. These are alternative policy
inputs, not approved replacements for the ranges above. Compliance must resolve them into one schedule
per data category, including archive expiry, legal holds and backups. The attribution of five years to
NIS2 Article 21 is not accepted as verified legal justification; the applicable legal basis and duration
must be confirmed. Nor is a provider "no data logging" opt-out assumed enabled without approval evidence.

</details>
