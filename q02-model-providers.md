# Q02 - Όριο παρόχων μοντέλων

**Πηγή:** [Q2 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** Azure OpenAI & Local/On-Premises AI slide

Οι κλήσεις περνούν από το Models plane (επίπεδο μοντέλων), αλλά τα SDKs (κιτ ανάπτυξης παρόχων) ανήκουν στους adapters (προσαρμογείς), όχι στον Router (δρομολογητή).

[HTML διάγραμμα](q02-model-providers.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] Εφαρμογή NCT-AI [BUILT]
  +-- κλήση --> [n2] Models / Router [BUILT]
  |   +-- επιλογή --> [n3] AzureOpenAI adapter [BUILT]
  |   |   +-- SDK --> [n4] Azure OpenAI [DEV]
  |   +-- εναλλακτική / στόχος --> [n5] Τοπικός adapter [INTERFACE]
  |   |   +-- εκκρεμεί / στόχος --> [n6] On-premises μοντέλο [DESIGNED]
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | Εφαρμογή NCT-AI | Ελεγχόμενη κλήση | BUILT (υλοποιημένο) |
| n2 | Models / Router | Ανεξάρτητα από πάροχο | BUILT (υλοποιημένο) |
| n3 | AzureOpenAI adapter | Εδώ απομονώνεται το SDK | BUILT (υλοποιημένο) |
| n4 | Azure OpenAI | Συνδεδεμένο στην ανάπτυξη | DEV (ανάπτυξη) |
| n5 | Τοπικός adapter | Δοκιμασμένη διεπαφή | INTERFACE (δοκιμασμένη διεπαφή) |
| n6 | On-premises μοντέλο | Εντός εγκαταστάσεων | DESIGNED (σχεδιασμένο) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | κλήση | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n2 -> n3 | επιλογή | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n3 -> n4 | SDK | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n2 -> n5 | εναλλακτική | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n5 -> n6 | εκκρεμεί | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |

## Τεκμηρίωση και όρια

- BUILT (υλοποιημένο): ουδέτερες συμβάσεις/Core, απομόνωση SDK και δοκιμές ορίων βιβλιοθηκών.
- Azure-managed (διαχειριζόμενο), hybrid (υβριδικό) και on-premises (εντός εγκαταστάσεων) χρειάζονται χωριστή αξιολόγηση.
- Τοπικό σύστημα παραγωγής δεν έχει τεκμηριωθεί. Η ανεξαρτησία παρόχου δεν εγγυάται γεωγραφία δεδομένων (Q15).
- Οι εκτιμήσεις 90% κόστους Azure και EUR 200K GPU CAPEX (αρχική επένδυση) δεν είναι προσφορές.

## Υπόμνημα

- BUILT (υλοποιημένο)
- DEV (ανάπτυξη)
- INTERFACE (δοκιμασμένη διεπαφή)
- DESIGNED (σχεδιασμένο)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** Every governed model call passes through the Models plane and Model Router; provider SDKs
are isolated inside provider adapters such as `NCT.AI.Models.AzureOpenAI`. The router and core
contracts are provider-neutral. The same application-facing APIs can support Azure-managed, hybrid
Azure/on-premises and fully on-premises inference without coupling business logic to a provider SDK.

**Current state.** Azure OpenAI is connected in development. The local-provider interface is tested,
but a Production local-inference backend is not yet evidenced. Assembly-boundary tests verify the SDK
separation (Appendix B1). Provider independence does not itself enforce data residency: chat and
embedding deployments have different processing scopes, as explained in Q15.

**Target / remaining gap.** Azure-managed inference avoids customer-managed GPUs; local inference
offers greater control of the data path but requires GPU investment, serving operations and capacity
planning. Hybrid adds two operating environments. Each option needs capability, security, quality and
regression validation, including private connectivity and permitted egress. The source's hybrid
estimate of approximately 90% of Azure runtime cost and approximately EUR 200K GPU CAPEX are
unvalidated planning assumptions, not quotations or demonstrated savings.

</details>
