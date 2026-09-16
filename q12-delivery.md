# Q12 - Από CI σε ελεγχόμενη παραγωγή

**Πηγή:** [Q12 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** Target CI/CD and deployment model; manual steps; release launch process

Οι έλεγχοι CI δεν ισοδυναμούν με επιτυχή, υπογεγραμμένη και εγκεκριμένη παραγωγική διάθεση.

[HTML διάγραμμα](q12-delivery.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] CI: build / tests [BUILT]
  +-- παραδοτέα / στόχος --> [n2] Υπογραφή εικόνων [BLOCKED]
  |   +-- διάθεση / στόχος --> [n3] DEV εγκατάσταση [DEV]
  |   |   +-- επικύρωση / στόχος --> [n4] UAT αποδοχή [DESIGNED]
  |   |   |   +-- αποδοχή / στόχος --> [n5] Έγκριση παραγωγής [PENDING]
  |   |   |   |   +-- εξουσιοδότηση / στόχος --> [n6] Παραγωγική διάθεση [DESIGNED]
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | CI: build / tests | Κατασκευή και δοκιμές | BUILT (υλοποιημένο) |
| n2 | Υπογραφή εικόνων | Επιτυχής έκδοση μη αποδεδειγμένη | BLOCKED (μπλοκαρισμένο) |
| n3 | DEV εγκατάσταση | AKS ανάπτυξης έχει λειτουργήσει | DEV (ανάπτυξη) |
| n4 | UAT αποδοχή | Ασφάλεια / απόδοση / επιχείρηση | DESIGNED (σχεδιασμένο) |
| n5 | Έγκριση παραγωγής | Υποχρεωτική πύλη επιβολής | PENDING (εκκρεμές) |
| n6 | Παραγωγική διάθεση | Επαλήθευση και επαναφορά | DESIGNED (σχεδιασμένο) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | παραδοτέα | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n2 -> n3 | διάθεση | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n3 -> n4 | επικύρωση | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n4 -> n5 | αποδοχή | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n5 -> n6 | εξουσιοδότηση | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |

## Τεκμηρίωση και όρια

- Το διάγραμμα δείχνει τη διαδρομή στόχου. Οι παρατηρημένες χειροκίνητες εγκαταστάσεις DEV δεν αποδεικνύουν ολοκλήρωσή της.
- images-signed και signatures-verified (υπογεγραμμένες εικόνες/επαληθευμένες υπογραφές) παραμένουν blocked (μπλοκαρισμένα).
- Release manifest (μητρώο έκδοσης): εφαρμογή, κανόνες, γνώση, μοντέλα, μεταβολές σχήματος, τεκμήρια και αναφορές επαναφοράς.
- Η παραγωγική έγκριση χρειάζεται επιβολή πριν ενεργοποιηθούν διαπιστευτήρια εγκατάστασης. Τα εργαλεία CI/CD επιλέγονται με Telekom.

## Υπόμνημα

- BUILT (υλοποιημένο)
- BLOCKED (μπλοκαρισμένο)
- DEV (ανάπτυξη)
- DESIGNED (σχεδιασμένο)
- PENDING (εκκρεμές)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** Delivery requires validated, signed artifacts, UAT acceptance, explicit Production
authorisation and post-deployment verification. CI mechanisms alone do not prove a completed release.

**Current state.** Build/test and security workflows exist; development AKS deployments are recorded.
Image-signing/verification mechanisms exist, but successful release evidence is still blocked.
Development deployment records do not establish an automated, governed Production deployment
pipeline. Implementation and current approval-gate constraints are in Appendix B4.

**Target / remaining gap.** The target sequence is CI validation, signed artifacts, DEV, UAT
regression/security/performance and business acceptance, Production approval, deployment and
verification. A release manifest must bind application/rules, agents, knowledge, prompts,
model/embedding configuration, migrations, test/scan evidence and rollback references.
Environment-dependent Oracle checks and Production migrations need evidence from the target
environment; non-production also needs startup/readiness checks after deliberate cluster stops.

Manual decisions remain: SME approval for outcome-changing knowledge/rules, business acceptance,
high-risk security review, release authorisation and emergency rollback. The Production approval
gate must be enforceable in the chosen delivery platform before deployment credentials are enabled.
The customer may choose its own CI/CD tooling in the Telekom tenant; Azure DevOps and GitLab CI are
options to agree, not verified existing NCTSite tools. The current GitHub setup is not the customer's
final deployment arrangement.

</details>
