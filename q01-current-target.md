# Q01 - Υφιστάμενο και νέο σύστημα

**Πηγή:** [Q1 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** Current State vs Target State slide, with new systems visible

Το NCT-AI προσθέτει ελεγχόμενη ευφυΐα γύρω από το NCTSite, χωρίς να αντικαθιστά τις επίσημες πηγές δεδομένων.

[HTML διάγραμμα](q01-current-target.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] NCTSite / Oracle [CURRENT]
  +-- ανάγνωση --> [n2] Ελεγχόμενη πρόσβαση [PARTIAL]
  |   +-- δεδομένα --> [n3] Εκτέλεση NCT-AI [MIXED]
  |   |   +-- αποτελέσματα --> [n4] Διεπαφή AG-UI [BUILT]

[n6] Έλεγχος λειτουργίας [MIXED]
  +-- έλεγχοι --> [n5] Γνώση και μοντέλα [MIXED]
  |   +-- στήριξη --> [n3] Εκτέλεση NCT-AI [MIXED] (αναφορά)
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | NCTSite / Oracle | Επίσημα δεδομένα | CURRENT (υφιστάμενο) |
| n2 | Ελεγχόμενη πρόσβαση | Oracle MCP / Tool Gateway | PARTIAL (μερικώς υλοποιημένο) |
| n3 | Εκτέλεση NCT-AI | Runtime / C# / Temporal | MIXED (μικτή κατάσταση ανά συνιστώσα) |
| n4 | Διεπαφή AG-UI | Gateway / εγκρίσεις | BUILT (υλοποιημένο) |
| n5 | Γνώση και μοντέλα | OKF / ανάκτηση / Router | MIXED (μικτή κατάσταση ανά συνιστώσα) |
| n6 | Έλεγχος λειτουργίας | Policy / audit / CI-CD | MIXED (μικτή κατάσταση ανά συνιστώσα) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | ανάγνωση | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n2 -> n3 | δεδομένα | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n3 -> n4 | αποτελέσματα | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n5 -> n3 | στήριξη | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n6 -> n5 | έλεγχοι | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |

## Τεκμηρίωση και όρια

- CURRENT (υφιστάμενο): WebForms/MVC, Oracle, SAP feed (τροφοδοσία), RP/TR και Entra ID παραμένουν.
- Οι 18 νέες συνιστώσες ομαδοποιούνται σε επίπεδα, όχι σε 18 ήδη παραγωγικές υπηρεσίες. AKS ανάπτυξης έχει επιδειχθεί, συνεχής παραγωγική διαθεσιμότητα όχι.
- Το επίπεδο γνώσης περιλαμβάνει compiler (μεταγλωττιστή), OKF repository (αποθετήριο), retrieval (ανάκτηση), PostgreSQL/pgvector και AGE. Μοντέλα μέσω Router και παρόχου, Redis για συνεδρίες.
- Οι εγγραφές είναι χωριστό θέμα Q16/Q19: σήμερα μόνο DryRun (δοκιμαστική εκτέλεση χωρίς εγγραφή).

## Υπόμνημα

- CURRENT (υφιστάμενο)
- PARTIAL (μερικώς υλοποιημένο)
- MIXED (μικτή κατάσταση ανά συνιστώσα)
- BUILT (υλοποιημένο)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** Yes. The presentation should distinguish the existing NCTSite/Oracle/external-systems
landscape from the eighteen new components NCT-AI introduces: AG-UI, API/BFF gateway, Agent Runtime,
Model Router, Knowledge Compiler, the Approved OKF Knowledge Bundle repository, retrieval services,
PostgreSQL/pgvector, graph knowledge via Apache AGE, the deterministic Rule Engine, Temporal workflow
orchestration, the MCP/Tool Gateway, the Oracle read-only MCP layer, the Policy/Guardrail service, the
Evidence/Audit/Trace subsystem, the model provider (Azure OpenAI or local), Azure Managed Redis, and
CI/CD/release controls. NCTSite and Oracle remain the authoritative systems of record; NCT-AI adds a
governed intelligence and execution layer around them rather than replacing them
(`nct-ai-architecture-design.md` §1–2, `nct-ai-azure-deployment.md` §1 carry the same distinction with
diagrams). The Azure implementation of this target is no longer only a paper architecture: an AKS
deployment of the NCT-AI service set has been executed and observed running.

The additional response explicitly retains NCTSite WebForms/MVC, Oracle, the SAP feed, RP/TR modules
and Entra ID. External systems remain in place; retaining them does not eliminate interface work.
The requested slide should show existing flows separately from new interfaces and phase-gated writes
(Q19–Q20). This questionnaire update does not itself deliver the revised presentation slides.

</details>
