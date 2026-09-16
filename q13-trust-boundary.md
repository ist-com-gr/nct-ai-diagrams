# Q13 - Μη έμπιστη είσοδος και όρια εξουσίας

**Πηγή:** [Q13 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** Protection against prompt injection and malicious retrieved content

Ούτε χρήστης ούτε ανακτημένο περιεχόμενο μπορεί να εκχωρήσει δικαιώματα ή να παρακάμψει πολιτική εκτέλεσης.

[HTML διάγραμμα](q13-trust-boundary.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] Πολιτική εκτέλεσης [REQUIRED]
  +-- περιορίζει --> [n2] Εξουσιοδότηση [REQUIRED]
  |   +-- επιτρέπει --> [n3] Typed εργαλεία [BUILT]
  |   |   +-- επικυρώνει --> [n4] Περιορισμένη εκτέλεση [REQUIRED]

[n6] Έλεγχοι ασφαλείας [PARTIAL]
  +-- δοκιμάζει / στόχος --> [n5] Χρήστης και ανάκτηση [UNTRUSTED]
  |   +-- δεδομένα --> [n4] Περιορισμένη εκτέλεση [REQUIRED] (αναφορά)
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | Πολιτική εκτέλεσης | Ανώτερο όριο εξουσίας | REQUIRED (απαίτηση) |
| n2 | Εξουσιοδότηση | Ρόλοι / συμβάσεις πράκτορα | REQUIRED (απαίτηση) |
| n3 | Typed εργαλεία | Allowlist / SELECT / όρια | BUILT (υλοποιημένο) |
| n4 | Περιορισμένη εκτέλεση | Επικύρωση ανεξάρτητα από LLM | REQUIRED (απαίτηση) |
| n5 | Χρήστης και ανάκτηση | Μη έμπιστα δεδομένα | UNTRUSTED (μη έμπιστο) |
| n6 | Έλεγχοι ασφαλείας | Κακόβουλα έγγραφα / συμβάντα | PARTIAL (μερικώς υλοποιημένο) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | περιορίζει | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n2 -> n3 | επιτρέπει | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n3 -> n4 | επικυρώνει | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n5 -> n4 | δεδομένα | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n6 -> n5 | δοκιμάζει | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |

## Τεκμηρίωση και όρια

- Typed requests (αιτήματα καθορισμένων τύπων), allowlists (λίστες επιτρεπόμενων), δεσμευμένες τιμές και όρια πόρων υπάρχουν στη διαδρομή Oracle.
- Δεν πρόκειται για SQL parser/AST filter (συντακτικό φίλτρο), ούτε για απόδειξη πλήρους ανοσίας σε prompt injection (έγχυση οδηγιών).
- Η εγκεκριμένη γνώση επίσης δεν παραχωρεί δικαιώματα. Κακόβουλο περιεχόμενο μπορεί να επηρεάσει επιτρεπόμενα επιχειρήματα ή διατύπωση.
- Σάρωση/καραντίνα, πλήρεις δοκιμές και δικαιώματα βάσης χρειάζονται ξεχωριστή τεκμηρίωση ανά περιβάλλον.

## Υπόμνημα

- REQUIRED (απαίτηση)
- BUILT (υλοποιημένο)
- UNTRUSTED (μη έμπιστο)
- PARTIAL (μερικώς υλοποιημένο)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** Neither user input nor retrieved content can override runtime/system policy,
authorisation, agent constraints or tool permissions. User input is also untrusted. Runtime policy
sets the outer boundary; authorisation and agent/tool contracts constrain the user's request.
Retrieved documents and tool results are untrusted data/evidence, not instructions that can change
that boundary. Even approved knowledge cannot grant tool permissions or override policy.

**BUILT controls on the Oracle path:** typed tool requests, dataset/column/operator allowlists,
server-side construction of SELECT queries, bound values and row/time/response-size limits. The tool
does not accept arbitrary SQL, so its protection is not an SQL parser/AST filter (Q16). Policy and
tool validation must remain effective regardless of what the model proposes.

The Oracle adversarial tests verify this structured-query boundary, not complete prompt-injection
resistance across the platform. Content scanning/quarantine at ingestion, malicious-document
evaluations, end-to-end identity restrictions and security-event coverage must each have separate
implementation and deployment evidence; they are not all established by the Oracle test suite.
The production-readiness gate `oracle-read-only-controls: met` has that narrower scope.
Prompt injection may still corrupt model output; deterministic evidence checks and human approval
remain necessary, and no claim of immunity is made.

Permitted tool arguments and model wording can still be influenced by malicious content. Negative
tests must cover that narrower failure mode as well as attempts to invoke forbidden tools. Database
write denial is a separate defence only after effective grants have been verified (Q16).

</details>
