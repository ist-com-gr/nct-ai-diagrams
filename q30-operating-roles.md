# Q30 - Ρόλοι και πραγματική λογοδοσία

**Πηγή:** [Q30 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** New roles required to operate NCT-AI

Κάθε πύλη έγκρισης χρειάζεται ονομαστικό υπεύθυνο, εξουσία και χρόνο εργασίας, όχι μόνο τίτλο ρόλου.

[HTML διάγραμμα](q30-operating-roles.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] Platform / Use Case Owner [PROPOSED]
  +-- ανάθεση --> [n2] Knowledge Author [PROPOSED]
  |   +-- επιθεώρηση --> [n3] SME / Knowledge Approver [PROPOSED]
  |   |   +-- αποδοχή --> [n4] Παραγωγική λειτουργία [PROPOSED]

[n6] Audit / συμμόρφωση [PROPOSED]
  +-- έλεγχος --> [n5] Ασφάλεια / δεδομένα [PROPOSED]
  |   +-- συμβάσεις --> [n4] Παραγωγική λειτουργία [PROPOSED] (αναφορά)
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | Platform / Use Case Owner | Ιδιοκτησία πλατφόρμας / σεναρίου | PROPOSED (προτεινόμενο) |
| n2 | Knowledge Author | Συντάκτης υποψήφιας γνώσης | PROPOSED (προτεινόμενο) |
| n3 | SME / Knowledge Approver | Ειδικός / εγκριτής γνώσης | PROPOSED (προτεινόμενο) |
| n4 | Παραγωγική λειτουργία | SRE / Model Ops / Rule Engineer | PROPOSED (προτεινόμενο) |
| n5 | Ασφάλεια / δεδομένα | IAM / Integration Owner | PROPOSED (προτεινόμενο) |
| n6 | Audit / συμμόρφωση | Ελεγκτής και πολιτική διατήρησης | PROPOSED (προτεινόμενο) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | ανάθεση | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n2 -> n3 | επιθεώρηση | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n3 -> n4 | αποδοχή | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n5 -> n4 | συμβάσεις | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n6 -> n5 | έλεγχος | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |

## Τεκμηρίωση και όρια

- PROPOSED (προτεινόμενοι) ρόλοι: η Telekom αποφασίζει πραγματικά πρόσωπα/μονάδες και διαχωρισμό συντάκτη από εγκριτή.
- Rule/Agent Engineer (μηχανικός κανόνων/πρακτόρων), Model Ops (λειτουργία μοντέλων) και SRE (μηχανική αξιοπιστίας) είναι διαφορετικές ευθύνες.
- Security/IAM Owner (υπεύθυνος ασφάλειας/ταυτοτήτων), Integration/Data Contract Owner (υπεύθυνος διεπαφών/συμβάσεων) και Compliance (συμμόρφωση) καλύπτουν χωριστές πύλες.
- Προστίθενται Knowledge Reader (αναγνώστης γνώσης), Structural/Acquisition/EMF owners (υπεύθυνοι περιορισμών) και Radio Planning Team Lead (επικεφαλής σχεδιασμού). Δεν συνεπάγονται αυτόματα νέες προσλήψεις.

## Υπόμνημα

- PROPOSED (προτεινόμενο)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** NCT-AI needs an operating model, not only technical components. Proposed roles: **AI
Platform Owner** (roadmap, service ownership, production priorities, SLO/budget — likely Telekom
IT/platform organisation); **AI Product/Use Case Owner** (use-case value, business acceptance criteria,
priority, release acceptance — the relevant engineering/business domain); **Knowledge Author** (source
registration, candidate knowledge curation — an engineering domain team or delegated knowledge team);
**SME Reviewer** (verifying technical meaning and vendor interpretation — a Telekom engineering SME);
**Knowledge Approver** (approving knowledge/rule releases for Production, confirming source authority —
a designated senior Telekom knowledge/domain owner); **Rule/Agent Engineer** (deterministic rule
representation, agent definitions, tool contracts, test automation — the NCT-AI engineering team);
**AI/Model Ops** (model registry, routing, validation, cost monitoring, model release — the AI platform
team); **SRE/Platform Operations** (AKS, PostgreSQL, Redis, Temporal, monitoring, HA/DR, patching,
incident management — Telekom IT/cloud/platform operations); **Security/IAM Owner** (Entra ID, workload
identity, service authorisation, security policy, vulnerability process — Telekom Cyber Security/IAM);
**Integration/Data Contract Owner** (NCTSite curated views, interface versions, external contracts,
upgrade compatibility — the NCTSite/system integration team); **Audit/Compliance Reviewer** (audit
policy, retention review, evidence requirements, compliance verification — Telekom Security/Compliance).
Every governance gate this document describes — SME approval before a knowledge release activates, an
Approver's sign-off, a Security owner for the mesh-attestation work in Q14 — needs a named person
behind it before Production, not only a role on a slide. Naming the actual people or organisational
units against this list is a Telekom decision. The additional response also identifies a Knowledge
Reader access role, Site Constraint owners (Structural, Acquisition and EMF), and a Radio Planning
Team Lead for quality/backlog review. Add these to the responsibility matrix without assuming they
require new hires or can all be absorbed by existing staff. Workload, authority and separation of
author/reviewer duties must be assigned explicitly.

</details>
