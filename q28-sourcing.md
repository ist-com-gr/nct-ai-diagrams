# Q28 - Επιλογές προμήθειας και βάση γράφου

**Πηγή:** [Q28 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** Open-source/self-hosted vs managed/commercial components

Το μικτό μοντέλο διαχειριζόμενων και ανοικτών υπηρεσιών χρειάζεται λειτουργική αξιολόγηση, όχι μόνο σύγκριση αδειών.

[HTML διάγραμμα](q28-sourcing.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] Επιλογή Telekom [PENDING]
  +-- επιλογή --> [n2] Open source [OPTION]
  |   +-- εναλλακτικές --> [n3] Managed Azure [OPTION]
  |   |   +-- πρότυπο --> [n4] Τρέχον πρότυπο γράφου [TARGET]

[n5] Neo4j Aura [HISTORICAL]
  +-- νεότερη απόφαση --> [n4] Τρέχον πρότυπο γράφου [TARGET] (αναφορά)
  +-- διατηρείται --> [n6] Adapter Neo4j [RETAINED]
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | Επιλογή Telekom | Λειτουργία / υποστήριξη / κόστος | PENDING (εκκρεμές) |
| n2 | Open source | .NET / PostgreSQL / Temporal | OPTION (εναλλακτική) |
| n3 | Managed Azure | AKS / Redis / AI / Storage | OPTION (εναλλακτική) |
| n4 | Τρέχον πρότυπο γράφου | Μόνο Apache AGE | TARGET (στόχος) |
| n5 | Neo4j Aura | Παλαιότερη χρήση ανάπτυξης | HISTORICAL (ιστορική χρήση) |
| n6 | Adapter Neo4j | Διαλειτουργικότητα / δοκιμές | RETAINED (διατηρείται) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | επιλογή | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n2 -> n3 | εναλλακτικές | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n3 -> n4 | πρότυπο | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n5 -> n4 | νεότερη απόφαση | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n5 -> n6 | διατηρείται | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |

## Τεκμηρίωση και όρια

- Open source (ανοικτός κώδικας) δεν σημαίνει μηδενικό TCO (συνολικό κόστος ιδιοκτησίας). Λειτουργία, αντίγραφα και ενημερώσεις παραμένουν.
- Neo4j χρησιμοποιήθηκε/αξιολογήθηκε στην ανάπτυξη. AGE-only (μόνο AGE) είναι σημερινός στόχος εγκατάστασης, όχι άρνηση του ιστορικού.
- Η δυνατότητα διακυβέρνησης γνώσης είναι απαραίτητη, όχι υποχρεωτικά η μορφή OKF. Git και μητρώο εγκρίσεων είναι εναλλακτική που απαιτεί προσαρμογή.
- Η επιλογή OKF δεν δικαιολογεί από μόνη της Knowledge Graph (γράφο γνώσης). Χρειάζονται συγκεκριμένα ερωτήματα/εξαρτήσεις.

## Υπόμνημα

- PENDING (εκκρεμές)
- OPTION (εναλλακτική)
- TARGET (στόχος)
- HISTORICAL (ιστορική χρήση)
- RETAINED (διατηρείται)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** The platform supports a mixed sourcing model. Open-source/self-hostable: .NET/ASP.NET,
Kubernetes, Istio, PostgreSQL, pgvector, Apache AGE (Apache 2.0), Temporal Server (MIT-licensed),
OpenTelemetry. Managed/commercial with an Azure equivalent: AKS (managed Kubernetes), Azure Database
for PostgreSQL (with pgvector and, on supported versions, Apache AGE), Azure Managed Redis, Azure
OpenAI/Foundry models, Entra ID, Azure Storage/ACR/Key Vault, and Azure Monitor/Dynatrace as an
OpenTelemetry backend option. Temporal Cloud exists as the managed alternative to self-hosting the MIT
server. Open source does not mean zero TCO — self-hosted components still need operations, backups,
monitoring, patching, capacity planning and engineering support. Final sourcing decisions belong to Telekom.

**Current target and history.** Neo4j was previously used/evaluated in development, including Aura
and live integration tests. The current target deployment standard is **Apache AGE only**; the Neo4j
adapter remains for interoperability/testing. This is a deployment decision, not a claim that Neo4j
was never deployed or a fresh inventory of running services. Appendix B7 separates the historical
evidence from the current standard.

**Target / remaining gap.** Confirm sourcing, supported versions and deployment compliance for each
customer environment before Production.
Provider abstraction reduces coupling; it does not eliminate migration effort or dependency on managed
identity, storage, networking and operational tooling. Existing subscriptions do not automatically
include every required entitlement or support contract.

For knowledge governance, **the capability is needed, not necessarily the specific OKF format**.
For a small, stable rule set, versioned documentation in Git plus a source/version/approval registry
could suffice. Replacing OKF would require adapting current contracts and preserving governance
checks, not simply deleting the knowledge files. OKF is more justified when many agents and scenarios
reuse the same curated knowledge through different retrieval methods. Choosing OKF does not itself
establish a need for a Knowledge Graph; graph-specific queries and dependencies must justify that
separate choice. These are design trade-offs, not a claim that the simpler replacement is implemented.

</details>
