# Q20 - Εξωτερικά συστήματα ανά σενάριο

**Πηγή:** [Q20 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** External systems/interfaces required per use case and phase (Atoll, mcSON, SAP, Ariadne)

Υπάρχουσα επιχειρησιακή εξάρτηση δεν ισοδυναμεί με νέο ή ήδη εγκατεστημένο connector (σύνδεσμο ενσωμάτωσης).

[HTML διάγραμμα](q20-external-systems.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] SAP νυχτερινή ροή [CURRENT]
  +-- τροφοδοσία --> [n2] NCTSite [CURRENT]
  |   +-- ανάγνωση --> [n3] NCT-AI σενάρια [MIXED]
  |   |   +-- απογραφή / στόχος --> [n4] Μήτρα διεπαφών [REQUIRED]

[n5] Atoll / mcSON [REQUIREMENT]
  +-- συμφωνία / στόχος --> [n4] Μήτρα διεπαφών [REQUIRED] (αναφορά)

[n6] eSurvey / Cronoss / OSS [PROPOSAL]
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | SAP νυχτερινή ροή | Υφιστάμενα υλικά / αποθήκη | CURRENT (υφιστάμενο) |
| n2 | NCTSite | Δεδομένα και προέλευση | CURRENT (υφιστάμενο) |
| n3 | NCT-AI σενάρια | Συμβάσεις και πολιτικές | MIXED (μικτή κατάσταση ανά συνιστώσα) |
| n4 | Μήτρα διεπαφών | Ρόλος / φάση / έγκριση / owner | REQUIRED (απαίτηση) |
| n5 | Atoll / mcSON | Γνωστές απαιτήσεις σεναρίων | REQUIREMENT (απαίτηση σεναρίου) |
| n6 | eSurvey / Cronoss / OSS | Προτεινόμενες συνδέσεις | PROPOSAL (πρόταση) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | τροφοδοσία | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n2 -> n3 | ανάγνωση | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n3 -> n4 | απογραφή | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n5 -> n4 | συμφωνία | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |

## Τεκμηρίωση και όρια

- SAP: RAN-02/AGG-04 και διαθεσιμότητα AGG-01 μέσω NCTSite όπου επαρκεί· όχι τεκμήριο άμεσου συνδέσμου SAP.
- Atoll: RAN-03/RAN-05. mcSON: RAN-05/RAN-06. Απαιτείται επιβεβαίωση κατεύθυνσης, συμβάσεων και πραγματικής υλοποίησης.
- eSurvey: RAN-03/RAN-05· Cronoss: RAN-05· OSS/NMS: AGG-06/MW-04. Ariadne προτείνεται εκτός τρέχουσας φάσης, υπό αποδοχή.
- Η Use Case × Interface matrix (μήτρα σεναρίων/διεπαφών) διακρίνει υπάρχουσα ροή, νέα πρόταση, read/write (ανάγνωση/εγγραφή), ιδιοκτήτη και κατάσταση.

## Υπόμνημα

- CURRENT (υφιστάμενο)
- MIXED (μικτή κατάσταση ανά συνιστώσα)
- REQUIRED (απαίτηση)
- REQUIREMENT (απαίτηση σεναρίου)
- PROPOSAL (πρόταση)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** Requirements are partly known from the scenarios; implementation status and interface
approval are separate questions. Absence of a direct NCT-AI connector does not make an existing
business-system dependency unknown.

| Scenario / system | Specified requirement or existing flow | NCT-AI implementation / outstanding decision |
|---|---|---|
| RAN-03 / Atoll | Planning outputs are a source; the scenario describes an existing, partly manual exchange with NCTSite | Direct automated integration is not evidenced; agree supported API/export, direction, cadence and owner with the SME |
| RAN-05 / mcSON | Licensing-table workflow includes NCTSite–mcSON exchange | Confirm the existing interface and whether NCT-AI needs direct access or consumes NCTSite data |
| RAN-02 and AGG-04 / SAP | Existing nightly SAP-to-NCTSite warehouse/material feed | Prefer the existing governed NCTSite data where adequate; a new direct SAP integration is not automatically required. Validate freshness and fields |
| AGG-01 / SAP-derived availability | Deterministic rules use availability facts | This does not establish a direct SAP connector; verify provenance and freshness through the supplied NCTSite facts |
| Ariadne | The additional response proposes exclusion from the current phase | Record as proposed out of scope, subject to customer acceptance; no confirmed connector requirement |
| RAN-05/RAN-06 / mcSON; RAN-03/RAN-05 / Atoll | The additional response proposes Phase 2 read/write and new-site exchanges | Confirm each scenario mapping, direction and existing/new contract; do not infer ready REST endpoints from this proposal |
| RAN-03/RAN-05 / eSurvey | The additional response lists site creation and licensing-table drafting | Proposed/in discussion; confirm owner, supported contract and delivery phase |
| RAN-05 / Cronoss | The additional response proposes an event-triggered Work Order/licensing-table push | Proposed Phase 2 dependency, not a deployed NCT-AI integration |
| AGG-06/MW-04 / OSS/NMS | The additional response proposes scheduled snapshots | Proposed Phase 2; agree exporter, freshness and ownership, without assuming live network control |

Sources: `submodules/NCT-AI/docs/specs/ran-03-new-site-requirements.md`,
`ran-05-licensing-table-requirements.md`, `ran-02-material-planning-requirements.md` and
`agg-04-warehouse-requirements.md` in the same directory. Scenario requirements are not evidence of
completed SME approval or a deployed integration.

The formal Use Case × Interface matrix should record the requirement, existing flow, proposed new
interface, read/write scope, owner, approval, implementation and deployment status, and delivery phase.
For any new connection, use the system's supported API/export/integration layer, register the tool,
and apply policy, timeout/retry, schema/version and audit controls. Do not introduce direct database
dependencies or an extra connector merely because a system appears in a scenario.

</details>
