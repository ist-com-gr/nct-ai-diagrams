# Q07 - Ρόλοι LLM και ανθρώπινη έγκριση

**Πηγή:** [Q7 στο ερωτηματολόγιο](source-answers.md) · 0.49-draft · 2026-09-16

**Ερώτηση πηγής (EN):** "Training/improvement" process and Human-in-the-Loop

Η βασική απόφαση AGG-01 δεν χρειάζεται LLM. Οι υλοποιημένες χρήσεις μοντέλου έχουν διαφορετικά όρια.

[HTML διάγραμμα](q07-llm-and-human.html) · [Ευρετήριο](index.md)

## Διάγραμμα κειμένου

```text
[n1] Δεδομένα και C# [BUILT]
  +-- αξιολόγηση --> [n2] Απόφαση κανόνων [BUILT]
  |   +-- διατύπωση --> [n3] Προαιρετική εξήγηση [GATED]
  |   |   +-- δεν εγκρίνει --> [n4] Ανθρώπινη έγκριση [REQUIRED]

[n5] Verify με μοντέλο [BUILT]
  +-- δεν εγκρίνει --> [n4] Ανθρώπινη έγκριση [REQUIRED] (αναφορά)

[n6] Γενική συνομιλία [BUILT]
```

Τα βέλη δηλώνουν μόνο την αναγραφόμενη σχέση. Δεν υπονοούν ότι όλη η διαδρομή έχει εγκατασταθεί. Η ένδειξη «αναφορά» δείχνει τον ίδιο κόμβο, όχι δεύτερη υπηρεσία.

## Κόμβοι και κατάσταση

| ID | Στοιχείο | Ερμηνεία | Κατάσταση |
|---|---|---|---|
| n1 | Δεδομένα και C# | Βασική απόφαση AGG-01 | BUILT (υλοποιημένο) |
| n2 | Απόφαση κανόνων | Χωρίς εκτέλεση LLM | BUILT (υλοποιημένο) |
| n3 | Προαιρετική εξήγηση | Μόνο ασαφές αποτέλεσμα | GATED (υπό προϋποθέσεις) |
| n4 | Ανθρώπινη έγκριση | Δεν χορηγείται από LLM | REQUIRED (απαίτηση) |
| n5 | Verify με μοντέλο | REJECT / NO-OBJECTION | BUILT (υλοποιημένο) |
| n6 | Γενική συνομιλία | Σύνθεση / προτάσεις εργαλείων | BUILT (υλοποιημένο) |

## Σχέσεις

| Από -> προς | Σχέση | Εύρος |
|---|---|---|
| n1 -> n2 | αξιολόγηση | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n2 -> n3 | διατύπωση | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n5 -> n4 | δεν εγκρίνει | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |
| n3 -> n4 | δεν εγκρίνει | Η σχέση της ετικέτας, όχι πρόσθετη πιστοποίηση παραγωγής |

## Τεκμηρίωση και όρια

- Και οι δύο AllowModelFallback (ρυθμίσεις άδειας μοντέλου) πρέπει να επιτρέπουν την κλήση για ClarificationRequired ή InsufficientEvidence.
- Το μοντέλο δεν αλλάζει απόφαση, βεβαιότητα ή τεκμήρια. Αποτυχία εξήγησης αφήνει τη διατύπωση κανόνων, εκτός ρητής ακύρωσης.
- Το Verify (βήμα εξέτασης) μπορεί να απορρίψει ή να αποτύχει, όχι να χορηγήσει ανθρώπινη έγκριση. Οι δύο χρήσεις δεν αποτελούν υποχρεωτική ενιαία ροή.
- Δεν υπάρχει αυτόματη εκπαίδευση από συνομιλίες. Ο πλήρης κύκλος ανατροφοδότησης παραμένει DESIGNED (σχεδιασμένος).

## Υπόμνημα

- BUILT (υλοποιημένο)
- GATED (υπό προϋποθέσεις)
- REQUIRED (απαίτηση)

Το χρώμα στο HTML διακρίνει είδος συνιστώσας, όχι βαθμό ετοιμότητας. Η κατάσταση γράφεται μέσα στον κόμβο. Οι διακεκομμένες σχέσεις δηλώνουν στόχο ή εκκρεμή προϋπόθεση.

<details>
<summary>Πλήρης απάντηση πηγής (EN), στιγμιότυπο 0.49-draft</summary>

**Answer.** The baseline architecture does not require continuous training or fine-tuning of the
foundation LLM. The improvement loop runs: production execution → engineer feedback/rejection →
analysis/clustering → candidate improvement (to knowledge, rules, prompt/policy, routing, Golden Set,
or retrieval configuration) → UAT regression → SME approval → controlled Production release. Model
fine-tuning is not part of the baseline lifecycle; it could be evaluated in the future for a narrowly
defined need, but would be treated as a separate model-development process with its own approved
training dataset, privacy/security review, lineage, validation, versioning, UAT testing, approval and
rollback plan — there is no "the model learns automatically from chats" behaviour. Human-in-the-Loop
governs approval of knowledge/rule changes, ambiguous technical interpretation, high-risk
recommendations, release gates, write/action workflows and exception handling. **BUILT** as an
architectural constraint (no code path for online learning exists in `src/NCT.AI.Models` or
`src/NCT.AI.Runtime`); the end-to-end feedback tooling remains **DESIGNED**.

No online learning does **not** mean identical model output on Day 1 and Day 400. Model inference is
also distinct from training. Its implemented roles differ by path:

| Path | Model role and limit | Current position |
|---|---|---|
| AGG-01 engineering decision | None: C# evaluates the facts and rules | The core decision needs no LLM inference |
| AGG-01 optional explanation | Rewords an already inconclusive result; cannot change decision, confidence or evidence | Implemented, gated by both request and use-case configuration |
| General chat | Synthesises the prompt, selected history and retrieved context; proposes available tool calls within bounded rounds | Implemented; activation and tools depend on configuration |
| Plan `Verify` step | Reviews prior artifacts and returns `REJECT` or `NO-OBJECTION` | Implemented; can block continuation, but cannot grant human approval |

In AGG-01, both `AllowModelFallback` settings must permit the call, and the rule result must be
`ClarificationRequired` or `InsufficientEvidence`. This fallback is for wording, **not a replacement
decision when a rule is missing**. If model generation fails or its wording is rejected, the rule-derived
explanation remains; explicit caller cancellation still cancels the operation. For example, a slot
reserved by a previous Work Order remains a clarification case; a model cannot waive the reservation.
Fixed response templates may suffice for this function, so any model benefit must be measured (Q21).

In a configured `Verify` step, rejection can decisively stop the flow. An unavailable, truncated or
unparseable verdict fails the step rather than passing unchecked. `NO-OBJECTION` is neither proof of
correctness nor an engineer's approval. The verifier has no Oracle/NCTSite tools; chat tool proposals
likewise grant no extra permissions. These are implemented paths, not evidence that every request or
environment uses them. Implementation evidence is in Appendix B3.

A two-person approval requirement must specify the action and enforce distinct authenticated
identities; it must not be presented as a proven check on every read-only answer simply because
engineer approval gates exist. **C# enforces the specified checks; the LLM helps interpret, synthesise
and communicate information without replacing those checks or human approval.**

</details>
