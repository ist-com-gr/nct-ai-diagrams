# Q11 - Ανεξάρτητη διεπαφή και NCTSite

**Πηγή:** [Q11 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** Dedicated GUI vs embedding into the existing NCTSite UI

Η σημερινή εφαρμογή AG-UI είναι ανεξάρτητη. Σύνδεσμος από NCTSite ή ενσωμάτωση είναι χωριστή μελλοντική επιλογή.

[HTML διάγραμμα](q11-user-interface.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] Μηχανικός [CURRENT]
  +-- είσοδος --> [n2] AG-UI / Entra ID [BUILT]
  |   +-- αιτήματα --> [n3] NCT-AI BFF / APIs [BUILT]

[n6] NCTSite σελίδα [CURRENT]
  +-- μελλοντικά / στόχος --> [n5] Deep link / context [DESIGNED]
  |   +-- έλεγχος / στόχος --> [n4] Έλεγχος ενσωμάτωσης [REQUIRED]
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | Μηχανικός | Πιστοποιημένος χρήστης | CURRENT (υφιστάμενο) |
| n2 | AG-UI / Entra ID | Ανεξάρτητο Blazor Server | BUILT (υλοποιημένο) |
| n3 | NCT-AI BFF / APIs | Συμβάσεις διεπαφής | BUILT (υλοποιημένο) |
| n4 | Έλεγχος ενσωμάτωσης | SSO / CSP / δικαιώματα | REQUIRED (απαίτηση) |
| n5 | Deep link / context | Σύνδεσμος με πλαίσιο οντότητας | DESIGNED (σχεδιασμένο) |
| n6 | NCTSite σελίδα | Καμία σημερινή σύνδεση AI | CURRENT (υφιστάμενο) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | είσοδος | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n2 -> n3 | αιτήματα | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n6 -> n5 | μελλοντικά | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |
| n5 -> n4 | έλεγχος | Στόχος/προϋπόθεση, όχι απόδειξη λειτουργίας |

## Τεκμηρίωση και όρια

- BUILT (υλοποιημένο): Blazor Server και MudBlazor με ξεχωριστή σύνδεση Entra ID, όχι ενσωματωμένο τμήμα NCTSite.
- DESIGNED (σχεδιασμένο): μενού/deep link (απευθείας σύνδεσμος), context passing (μεταφορά πλαισίου), προαιρετικό embedded component (ενσωματωμένο συστατικό).
- Ο πελάτης αποφασίζει τελική εμπειρία. Χρειάζονται έλεγχοι SSO/session (ενιαίας σύνδεσης/συνεδρίας), CSP (πολιτικής ασφάλειας περιεχομένου) και κύκλου υποστήριξης.
- Το υποτιθέμενο χαμηλό κόστος ενσωμάτωσης δεν είναι εκτιμημένη δέσμευση.

## Υπόμνημα

- CURRENT (υφιστάμενο)
- BUILT (υλοποιημένο)
- REQUIRED (απαίτηση)
- DESIGNED (σχεδιασμένο)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** **BUILT, as a dedicated application.** NCT-AI's AG-UI is a separate Blazor Server web
application with its own Entra ID sign-in (app registration `NCT`), not an embedded component inside
NCTSite — this describes the implemented baseline, not a restriction on the customer's final UX choice:
`nct-ai-technology-choices.md` §1 records "UI: Blazor
Server + MudBlazor, rejecting a React/Angular SPA embedded in NCTSite" specifically to keep one language
across the stack and typed component contracts rather than model-generated HTML inside an existing
page. The recommended phased model keeps Phase 1 as the dedicated NCT-AI UI talking to the NCT-AI
BFF/APIs directly — faster independent delivery, an independent release lifecycle, a clean security
boundary, and room to experiment with AI-specific UX (chat, approvals, traces, recommendation views) —
with Phase 2 integrating into NCTSite via menu/deep link, context passing, SSO, entity ids, and
optionally an embedded component/micro-frontend if NCTSite allows it (e.g. an "Ask NCT-AI" action on an
NCTSite equipment page opening NCT-AI pre-scoped to that equipment/context id). The recommendation is
not to couple the AI frontend's lifecycle tightly to the NCTSite monolith from the start; deep-linking
from NCTSite into a specific NCT-AI context is **DESIGNED**, not built — no NCTSite page currently links
out to NCT-AI. The customer can still choose the final access experience. A link with approved context
passing is different from an embedded panel: embedding requires SSO/session, framing/CSP, authorisation
and support-lifecycle review. The source's "practically inexpensive" description is not an assessed
implementation estimate.

</details>
