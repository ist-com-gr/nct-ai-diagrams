# Q26 - Ενημερώσεις ασφαλείας και υπογραφές

**Πηγή:** [Q26 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** Vulnerability patching and security updates for containers and platform components

Επιτυχής σάρωση ευπαθειών δεν αποδεικνύει υπογεγραμμένη εικόνα ή επιβολή πολιτικής στο cluster (συστάδα).

[HTML διάγραμμα](q26-security-patching.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] SBOM και scans [BUILT]
  +-- εύρημα --> [n2] Εκτίμηση CVE [REQUIRED]
  |   +-- προτεραιότητα / στόχος --> [n3] Διόρθωση και δοκιμές [REQUIRED]
  |   |   +-- παραδοτέο / στόχος --> [n4] Υπογραφή / επαλήθευση [BLOCKED]
  |   |   |   +-- υπογραφή / στόχος --> [n5] Admission / εγκατάσταση [VERIFY]
  |   |   |   |   +-- συντήρηση / στόχος --> [n6] Παρακολούθηση εκδόσεων [REQUIRED]
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | SBOM και scans | Κατάλογος λογισμικού / σαρώσεις | BUILT (υλοποιημένο) |
| n2 | Εκτίμηση CVE | Σοβαρότητα / έκθεση / ιδιοκτήτης | REQUIRED (απαίτηση) |
| n3 | Διόρθωση και δοκιμές | Έκτακτη ή τακτική συντήρηση | REQUIRED (απαίτηση) |
| n4 | Υπογραφή / επαλήθευση | Μη αποδεδειγμένη επιτυχής διάθεση | BLOCKED (μπλοκαρισμένο) |
| n5 | Admission / εγκατάσταση | Πολιτική εισαγωγής εικόνων | VERIFY (προς επαλήθευση) |
| n6 | Παρακολούθηση εκδόσεων | AKS / .NET / βάσεις / mesh | REQUIRED (απαίτηση) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | εύρημα | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n2 -> n3 | προτεραιότητα | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n3 -> n4 | παραδοτέο | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n4 -> n5 | υπογραφή | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n5 -> n6 | συντήρηση | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |

## Τεκμηρίωση και όρια

- BUILT (υλοποιημένο): έλεγχοι ασφαλείας CI. images-signed και signatures-verified παραμένουν blocked (μπλοκαρισμένα).
- 24-72 ώρες για κρίσιμα εκμεταλλεύσιμα προβλήματα όπου εφικτό είναι στόχος συζήτησης, όχι δεσμευτικό SLA.
- Εξαίρεση CVE (γνωστής ευπάθειας) απαιτεί υπεύθυνο, αιτιολόγηση, αντισταθμιστικό έλεγχο, λήξη και επανεκτίμηση.
- Η παύση DEV cluster δεν καταργεί ενημερώσεις εικόνων/εξαρτήσεων. Οι προτεινόμενες εβδομαδιαίες/μηνιαίες/τριμηνιαίες συχνότητες δεν είναι επαληθευμένο πρόγραμμα.

## Υπόμνημα

- BUILT (υλοποιημένο)
- REQUIRED (απαίτηση)
- BLOCKED (μπλοκαρισμένο)
- VERIFY (προς επαλήθευση)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** CI includes SBOM and vulnerability/security scanning controls; the readiness record marks
`vulnerability-gates: met`. Successful scans are distinct from image signing and admission enforcement.
Signing/verification mechanisms exist, but `images-signed` and `signatures-verified` remain `blocked`:
a successful signed-image release and verification have not been demonstrated. Production admission
policy, minimal/non-root containers and immutable image references must be verified in the target
cluster rather than inferred from a passing scan. The patch lifecycle assesses each detected CVE for
severity/exposure:
a critical/actively exploitable issue triggers an emergency release, a high-severity issue triggers an
accelerated patch, and medium/low issues follow normal maintenance — recommended operating targets for
discussion are 24–72 hours for critical exploitable issues where feasible, an agreed SLA for
high-severity issues, and scheduled monthly maintenance for
routine dependencies/base images. Platform-component versions are tracked: AKS, Kubernetes/node images,
PostgreSQL minor versions, Temporal releases, Redis service lifecycle, Istio/mesh versions, the .NET
runtime, ingress/gateway, and base Linux images. An accepted CVE exception needs an owner, a business
reason, a compensating control, an expiration date and a reassessment. This applies to the already-
deployed AKS workload estate, not only future containers: for the non-production cluster, vulnerability
management continues against the image registry/SBOM even while compute is stopped — a stopped cluster
reduces runtime cost, not the need to patch stored images and dependencies before the next startup.
Image signing (`images-signed`) has not yet executed in a workflow, though the identity to sign now
exists; the exact patch SLA follows Telekom policy. The new response's weekly image rebuilds,
quarterly Kubernetes upgrades and monthly node patching are candidate maintenance cadences, not
verified schedules. Assign owners, support-window constraints and emergency exceptions; a registry
push or managed-service label alone does not prove the corresponding security gate or patch schedule.

</details>
