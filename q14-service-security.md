# Q14 - Ασφάλεια μεταξύ υπηρεσιών

**Πηγή:** [Q14 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** Service-to-service authentication and encryption in Production

Η ασφάλεια παραγωγής αποδεικνύεται από εγκατεστημένη πολιτική και δοκιμές, όχι από πλήθος έτοιμων κοντέινερ.

[HTML διάγραμμα](q14-service-security.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] Υπηρεσία Α [TARGET]
  +-- ταυτότητα / στόχος --> [n2] Istio / STRICT mTLS [TARGET]
  |   +-- πολιτική / στόχος --> [n3] Υπηρεσία Β [TARGET]
  |   |   +-- TLS / στόχος --> [n4] Εξωτερικές εξαρτήσεις [TARGET]

[n6] Θετικές / αρνητικές δοκιμές [VERIFY]
  +-- επαλήθευση / στόχος --> [n5] Πολιτικές και bindings [VERIFY]
  |   +-- επιβολή / στόχος --> [n2] Istio / STRICT mTLS [TARGET] (αναφορά)
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | Υπηρεσία Α | Ταυτότητα ανά φόρτο | TARGET (στόχος) |
| n2 | Istio / STRICT mTLS | Αμοιβαία ταυτοποίηση και TLS | TARGET (στόχος) |
| n3 | Υπηρεσία Β | Ρητή επιτρεπόμενη επικοινωνία | TARGET (στόχος) |
| n4 | Εξωτερικές εξαρτήσεις | TLS / private endpoints | TARGET (στόχος) |
| n5 | Πολιτικές και bindings | PeerAuthentication / ταυτότητες | VERIFY (προς επαλήθευση) |
| n6 | Θετικές / αρνητικές δοκιμές | Επιτρεπτή σύνδεση / άρνηση | VERIFY (προς επαλήθευση) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | ταυτότητα | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n2 -> n3 | πολιτική | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n3 -> n4 | TLS | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n5 -> n2 | επιβολή | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n6 -> n5 | επαλήθευση | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |

## Τεκμηρίωση και όρια

- TARGET (στόχος): συνεχής παραγωγική τοπολογία. Υπάρχει βάση AKS/Istio, όχι απόδειξη καθολικής επιβολής.
- Τεκμήρια: istio-proxy, PeerAuthentication STRICT, AuthorizationPolicy, NetworkPolicies και bindings (δεσμεύσεις ταυτότητας).
- Ελέγχονται ιδιωτική συνδεσιμότητα, εξερχόμενη κίνηση και ανανέωση πιστοποιητικών/tokens (διακριτικών).
- SPIFFE/SPIRE attestation (επαλήθευση ταυτότητας φόρτου) και Entra Workload Identity (ταυτότητα φόρτου) είναι χωριστοί μηχανισμοί.

## Υπόμνημα

- TARGET (στόχος)
- VERIFY (προς επαλήθευση)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** The Production baseline requires private networking, per-service identity, explicit
service-to-service authorisation, STRICT mutual TLS within the mesh, network restrictions and TLS
to external dependencies. Workload/managed identity is preferred to shared or stored credentials.

**Current state.** The repository records the AKS service decomposition and an Istio implementation
foundation. This does not establish enforcement throughout a continuously available Production
topology. Container counts or readiness alone are not evidence of a mesh security control.

**Target / remaining gap.** Evidence must identify the deployed `istio-proxy` containers in the
sidecar-based topology, effective `PeerAuthentication` in `STRICT` mode, explicit
`AuthorizationPolicy` allow rules, NetworkPolicies, Workload Identity bindings, private endpoints,
restricted egress and certificate/token rotation. Positive and negative connectivity tests must
prove permitted traffic works and unauthorised service connections are refused, including relevant
external dependencies such as PostgreSQL, Oracle, Redis and model/API endpoints.

SPIFFE/SPIRE attestation and Entra Workload Identity bindings are separate mechanisms. Neither a
sidecar nor an Azure identity binding alone proves attestation, STRICT mTLS or coverage of all
outbound traffic. Each claim needs deployed configuration and test evidence for its environment.

</details>
