# Q19 - Δύο διαδρομές ανάγνωσης NCTSite

**Πηγή:** [Q19 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** New NCTSite APIs/views/interfaces required for integration

Το AGG-01 διαβάζει μέσω Oracle MCP ή PoTP REST. Η γνώση OKF δεν είναι τρίτη ζωντανή ανάγνωση NCTSite.

[HTML διάγραμμα](q19-nctsite-interfaces.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n3] PoTP REST / NCTSite [PARTIAL]
  +-- δεδομένα --> [n2] AGG-01 [CURRENT]

[n4] Work Order POST [PARTIAL]

[n5] Γνώση OKF [BUILT]
  +-- τεκμήρια --> [n2] AGG-01 [CURRENT] (αναφορά)

[n6] Αποδοχή συμβάσεων [REQUIRED]
  +-- επικύρωση / στόχος --> [n1] Oracle MCP / NCTSite [PARTIAL]
  |   +-- δεδομένα --> [n2] AGG-01 [CURRENT] (αναφορά)
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | Oracle MCP / NCTSite | Επιμελημένες όψεις | PARTIAL (μερικώς υλοποιημένο) |
| n2 | AGG-01 | Συμβουλευτικός πιλοτικός | CURRENT (υφιστάμενο) |
| n3 | PoTP REST / NCTSite | Έξι συμβάσεις GET | PARTIAL (μερικώς υλοποιημένο) |
| n4 | Work Order POST | Χωριστός πελάτης / μόνο DryRun | PARTIAL (μερικώς υλοποιημένο) |
| n5 | Γνώση OKF | Δεν είναι ζωντανή ανάγνωση | BUILT (υλοποιημένο) |
| n6 | Αποδοχή συμβάσεων | Εξουσιοδότηση και περιβάλλον | REQUIRED (απαίτηση) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | δεδομένα | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n3 -> n2 | δεδομένα | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n5 -> n2 | τεκμήρια | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n6 -> n1 | επικύρωση | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |

## Τεκμηρίωση και όρια

- GET: creation-eligibility, shelf-context, installed-equipment, interfaces, slots και next-code κάτω από api/v1/potp-mux.
- Η ύπαρξη πελάτη/δοκιμών δεν αποδεικνύει λειτουργία κάθε endpoint (σημείου πρόσβασης) στην παραγωγή.
- Το POST work-orders είναι χωριστό από τη σύνδεση ανάγνωσης Oracle, με έγκριση/πολιτική και χωρίς ενεργοποιημένη εγγραφή.
- Οι προτάσεις Φάσης 2 ή 10-15 όψεων ανά σενάριο δεν είναι εγκεκριμένο αντικείμενο ή επαληθευμένη απογραφή.

## Υπόμνημα

- PARTIAL (μερικώς υλοποιημένο)
- CURRENT (υφιστάμενο)
- BUILT (υλοποιημένο)
- REQUIRED (απαίτηση)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** AGG-01 already has two operational read integration paths in code: curated Oracle views
through the Oracle MCP server, and typed NCTSite REST reads through the PoTP MCP/API path. Knowledge
retrieval provides supporting approved content; it is not a third live read of NCTSite.

The existing PoTP REST client contracts use base path `api/v1/potp-mux`:

| Method | Relative path | Purpose |
|---|---|---|
| GET | `components/{id}/creation-eligibility` | Creation eligibility |
| GET | `components/{id}/shelf-context` | Shelf context |
| GET | `components/{id}/installed-equipment` | Installed equipment |
| GET | `cards/{id}/interfaces` | Card interfaces |
| GET | `chassis/{id}/slots` | Chassis slots |
| GET | `next-code` | Next-code lookup |
| POST | `work-orders` | Separate approval/policy-gated Work Order command; currently DryRun only (Q16) |

**Partially BUILT:** these client contracts and the curated Oracle integration. The existence of a
client or mocked contract test does not prove every endpoint is authorised and operational in
Production. The Work Order command is not part of the read-only Oracle connection.

Additional per-use-case curated views, entity-context/deep-link integration, user/role/scope mapping,
optional change events and contract-version negotiation require agreement with the NCTSite owner.
These broader integrations remain **DESIGNED** unless separately evidenced. Keep the stable
view/API contract independent of NCTSite's internal table layout.

**Proposed delivery scope:** Phase 1 is an advisory-only AGG-01 pilot. The source's Phase 2 write
proposal is not approved scope or permission to execute writes. The current decision remains
DryRun-only (Q16); changing it requires a separate owner decision and acceptance, regardless of phase.
Enforce this restriction through code/deployment policy and permissions, not just UI.
The new response also requests approval before sensitive reads and auditable readback: agree the
dataset classification, approver and enforcement point; do not infer these controls from a view alone.
Its estimates of 10–15 views per scenario and approximately 200 overall are not a verified inventory.
Count reusable data contracts first. The proposed `NCTAI_WO_WRITE` account must not be provisioned
under the current DryRun-only decision; its name is not evidence of an API identity or effective grants.

</details>
