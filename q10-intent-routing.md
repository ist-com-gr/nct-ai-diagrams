# Q10 - Πρόθεση, πράκτορας και επιτρεπόμενη γνώση

**Πηγή:** [Q10 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** Intent → Agent → allowed Knowledge Bundle mapping; ambiguous/no-match requests

Η σημερινή δρομολόγηση είναι ντετερμινιστική αντιστοίχιση φράσεων, όχι ταξινόμηση από LLM.

[HTML διάγραμμα](q10-intent-routing.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] Αίτημα χρήστη [INPUT]
  +-- φράσεις --> [n2] Intent router [BUILT]
  |   +-- υποψήφιος --> [n3] Έλεγχος πρόσβασης [BUILT]
  |   |   +-- επιτρέπεται --> [n4] Agent και σχέδιο [BUILT]
  |   |   |   +-- όρια --> [n5] Επιτρεπόμενη γνώση [BUILT]
  |   +-- δεν βρέθηκε --> [n6] Αποτυχία διαδρομής [BUILT]
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | Αίτημα χρήστη | Ή ρητή επιλογή πράκτορα | INPUT (είσοδος) |
| n2 | Intent router | Δρομολογητής φράσεων | BUILT (υλοποιημένο) |
| n3 | Έλεγχος πρόσβασης | Υπαρκτός / ενεργός / ρόλοι | BUILT (υλοποιημένο) |
| n4 | Agent και σχέδιο | Δηλωμένες συνταγές | BUILT (υλοποιημένο) |
| n5 | Επιτρεπόμενη γνώση | Bundle και δικαιώματα εργαλείων | BUILT (υλοποιημένο) |
| n6 | Αποτυχία διαδρομής | No-match / άρνηση πρόσβασης | BUILT (υλοποιημένο) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | φράσεις | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n2 -> n3 | υποψήφιος | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n3 -> n4 | επιτρέπεται | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n4 -> n5 | όρια | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n2 -> n6 | δεν βρέθηκε | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |

## Τεκμηρίωση και όρια

- Ρητή επιλογή πράκτορα παρακάμπτει τον εντοπισμό πρόθεσης, όχι ελέγχους ύπαρξης, ενεργοποίησης και ρόλων.
- Επικαλυπτόμενες φράσεις: πρώτη αντιστοίχιση. Δεν υπάρχει ανιχνευτής ασάφειας βάσει βεβαιότητας.
- ROUTE_NO_MATCHING_INTENT δεν είναι InsufficientEvidence (ανεπαρκή τεκμήρια ήδη επιλεγμένου πράκτορα).
- Διπλότυπες δηλωμένες προθέσεις απορρίπτονται στη φόρτωση. Μεταγενέστερο Verify δεν κάνει τον planner (σχεδιαστή) LLM-driven (καθοδηγούμενο από μοντέλο).

## Υπόμνημα

- INPUT (είσοδος)
- BUILT (υλοποιημένο)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** **BUILT:** manifests declare agent intents, roles and capabilities; deployment configuration
and knowledge authority constrain which providers and approved knowledge an execution can use. The
caller cannot grant itself a different corpus or extra tool permissions. Duplicate declared intents
are rejected when loading manifests (`CONFIG.md`, "the open agent set").

The current intent router performs deterministic phrase matching, not LLM classification. An
explicit agent choice skips intent inference but still checks whether the agent exists, is enabled
and permits the caller's roles. No match returns `ROUTE_NO_MATCHING_INTENT`; missing, disabled and
forbidden agents have separate routing outcomes. The plan builder then uses declared recipes without
a model call. Implementation evidence is in Appendix B3.

Overlapping phrase groups currently use the first match in configured order. That is distinct from a
duplicate manifest intent and is not a confidence-based ambiguity detector. Targeted clarification for
such ambiguity remains a design/acceptance requirement. A routing failure is also different from an
agent's `InsufficientEvidence` result after it has been selected; the two must not be conflated.

A later model-backed `Verify` step does not make the planner model-driven. Similarly, a chat model's
tool selection is not the registry's intent-routing algorithm. A conversational interface or multiple
agents alone is not evidence of LLM-based planning or routing (Q7).

</details>
