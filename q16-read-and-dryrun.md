# Q16 - Ανάγνωση Oracle και όριο εγγραφών

**Πηγή:** [Q16 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** Technical enforcement that the Oracle read path is read-only, and separation from the Work Order write path

Η ανάγνωση Oracle είναι χωριστή από τον πελάτη Work Order. Η ολοκληρωμένη διαδρομή εκτελείται μόνο ως DryRun, χωρίς πραγματική εγγραφή.

[HTML διάγραμμα](q16-read-and-dryrun.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] Typed QueryRequest [BUILT]
  +-- επικύρωση --> [n2] SELECT και παράμετροι [BUILT]
  |   +-- SELECT --> [n3] Oracle ανάγνωση [DEV]

[n6] Μηχανικός / πολιτική [PARTIAL]
  +-- έγκριση --> [n5] Work Order DryRun [COMPLETE DRYRUN]
  |   +-- δεν γράφει / στόχος --> [n4] Πραγματική εγγραφή [NOT APPROVED]
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | Typed QueryRequest | Καθορισμένο αίτημα ανάγνωσης | BUILT (υλοποιημένο) |
| n2 | SELECT και παράμετροι | Λίστες επιτρεπόμενων / όρια | BUILT (υλοποιημένο) |
| n3 | Oracle ανάγνωση | Grants προς επαλήθευση DBA | DEV (ανάπτυξη) |
| n4 | Πραγματική εγγραφή | Δεν έχει εκτελεστεί | NOT APPROVED (μη εγκεκριμένο) |
| n5 | Work Order DryRun | Προεπισκόπηση χωρίς εγγραφή | COMPLETE DRYRUN (ολοκληρωμένο μόνο χωρίς εγγραφή) |
| n6 | Μηχανικός / πολιτική | Χωριστός πελάτης HTTP | PARTIAL (μερικώς υλοποιημένο) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | επικύρωση | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n2 -> n3 | SELECT | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n6 -> n5 | έγκριση | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n5 -> n4 | δεν γράφει | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |

## Τεκμηρίωση και όρια

- Η ένδειξη COMPLETE DRYRUN αφορά την τεκμηριωμένη διαδρομή της 2026-09-11, όχι πραγματικό NCTSite write (εγγραφή).
- PARTIALLY BUILT (μερικώς υλοποιημένη) πραγματική εγγραφή: πελάτης, σύμβαση, ταυτοδυναμία και δοκιμές υπάρχουν. Σήμερα απαγορεύεται η δημιουργία NCTAI_WO_WRITE.
- Η απόφαση μόνο DryRun δεν επιβάλλεται ακόμη ως υποχρεωτικός κανόνας κώδικα/εγκατάστασης. Αυτό παραμένει ρητή εκκρεμότητα.
- Μελλοντική εγγραφή θέλει νέα απόφαση υπευθύνου, δικαιώματα, έγκριση μηχανικού, audit και πλήρη αποδοχή. Η επιστροφή Submitted/id δεν αποδεικνύει εγγραφή.

## Υπόμνημα

- BUILT (υλοποιημένο)
- DEV (ανάπτυξη)
- NOT APPROVED (μη εγκεκριμένο)
- COMPLETE DRYRUN (ολοκληρωμένο μόνο χωρίς εγγραφή)
- PARTIAL (μερικώς υλοποιημένο)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** Oracle reads and Work Order commands are separate paths. The read tool accepts typed,
allowlisted requests, constructs SELECT queries server-side, binds values and enforces resource
limits. The Work Order client does not reuse that connection or accept arbitrary SQL.

**Current state, reads: BUILT and tested at the application boundary.** No SQL parser/AST validator
or explicit `READ ONLY` transaction is claimed. The intended database account has session access
and SELECT on approved business objects, without business-object write privileges. Effective grants,
inherited roles and other privileges require separate DBA verification in each target database.
A documented development read is not a Production grant audit (Appendix B5).

**Current state, actual writes: PARTIALLY BUILT.** The client, HTTP contract, approval/policy checks,
idempotency handling and tests exist. The plan of record marks the governed end-to-end path complete
**for DryRun only**: preview execution, not persistence of a Work Order in NCTSite. The platform has
not executed a real NCTSite write. The current operating decision permits DryRun only and prohibits
provisioning the proposed write account; this is not an operational write capability.

**Target / remaining gap.** The DryRun-only operating decision still needs a matching enforceable
code/deployment invariant; the plan explicitly records that gap. Actual write execution is neither
approved nor deployed. Any future change requires a new explicit owner decision, verified API
authorisation, identities and grants, engineer approval, idempotency/audit controls and live
end-to-end acceptance. It is not authorised simply by completing DryRun or changing a configuration
flag. Appendix B5 distinguishes the completed DryRun evidence from older pending-status entries.

</details>
