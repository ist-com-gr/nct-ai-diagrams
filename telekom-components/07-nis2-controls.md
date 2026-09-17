# NCT-AI / NIS2 Use-Case Controls

[Diagram index](index.md) | [Interactive HTML](07-nis2-controls.html) | [JSON specification](https://github.com/ist-com-gr/NCT-AI/blob/b83199798e25d1312f5fff95a36db386f72bb326/design/Prerequisites/diagram-lab/telekom-components/specs/07-nis2-controls.json)

**Source:** [Telekom components overview](https://github.com/ist-com-gr/NCT-AI/blob/b83199798e25d1312f5fff95a36db386f72bb326/design/Components/NCT-AI_Components_Overview_Telekom_2026-09-17.md), sections 5, 6. Snapshot: 2026-09-17.

**View:** Governance model. Assess each scenario within the covered entity's operations and agree controls, evidence and accepting owners.

## Text Diagram

```text
[scenario] Use-case scope
  --- assess --> [risk] Risk assessment

[risk] Risk assessment
  --- select --> [controls] Proportionate controls

[controls] Proportionate controls
  --- verify --> [evidence] Test evidence

[evidence] Test evidence
  --- review --> [owner] Accepting owner

[owner] Accepting owner
  --- agree policy --> [retention] Retention policy
```

Arrows mean only the labeled relationship. They do not certify live traffic or a complete request trace.

## Components

| ID | Component | Context | Status / grouping |
|---|---|---|---|
| scenario | Use-case scope | PoTP / Oracle / knowledge / chat | COVERED ENTITY |
| risk | Risk assessment | Data / permissions / impact | PER SCENARIO |
| controls | Proportionate controls | Access / validation / continuity | DESIGN + OPERATE |
| retention | Retention policy | Purpose / category / legal basis | NO BLANKET 5 YEARS |
| owner | Accepting owner | Telekom IT / security / legal | EXPLICIT DECISION |
| evidence | Test evidence | Access / restart / restore / audit | VERIFY IN PILOT |

## Relationships

| From -> to | Meaning | Scope |
|---|---|---|
| scenario -> risk | assess | Relationship as labeled |
| risk -> controls | select | Relationship as labeled |
| controls -> evidence | verify | Relationship as labeled |
| evidence -> owner | review | Relationship as labeled |
| owner -> retention | agree policy | Relationship as labeled |

## Evidence and Limits

- The source maps AGG-01 recommendations to data/rule integrity, Oracle reads to access and leakage, knowledge publication to source integrity, external LLM use to disclosure/manipulation, and future Work Order writes to authorization and duplicate suppression.
- These are indicative technical controls, not a statutory list of required products or a declaration that every control is already operational.
- Article 21 does not itself establish a universal five-year audit-retention obligation. Agree retention by category and purpose, with any applicable separate legal or contractual basis.
- The source records Audit__Enabled=true for AG-UI, Chat, Context, Memory, Runtime and Workflow. Actual coverage, restart survival and tamper protection require pilot evidence.
- The obligation belongs to the entity within the applicable legal scope. Confirm applicability with Telekom legal/security; neither pilot nor read-only status supplies an automatic exemption.
- This is a governance dependency map, not a runtime sequence. Retention policy belongs in design before operation; its position here does not defer that decision until after testing.

## Source Snapshot

The following excerpt is attributed to the source document, not newly verified live evidence. Any unresolved claims are qualified above.

<details>
<summary>Source sections 5, 6</summary>

## 5. Data Residency & Compliance

- **EU residency is already the proven posture**: every managed
  dependency measured in dev is EU-hosted — PostgreSQL (West Europe),
  Oracle ADB (Frankfurt), Redis (West Europe), Temporal (West
  Europe). Nothing observed sends data outside the EU.
- **NIS2 applies to Telekom's use cases, not only to Kubernetes,
  databases and infrastructure.** The obligation sits with the covered
  entity (Telekom), and NCT-AI's use cases are assessed as part of
  Telekom's own systems and processes, risk-proportionate. The European
  Commission's official guidance confirms Art. 21(1) covers **all**
  functions and services of the entity, not only specific systems or
  critical services (¶7 of the guidance). Neither "read-only" nor
  "pilot" is an automatic exemption — both can lower risk and shape
  proportionate measures, but neither removes the obligation.

  | NCT-AI scenario | Risk | Indicative controls |
  |---|---|---|
  | PoTP/MUX recommendation (AGG-01) | Corrupted data or rules drive a dangerous recommendation | Version-controlled config, data provenance, testing, decision logging |
  | NCTSite read via Oracle MCP | Unauthorized access or leakage of network data | Least privilege, query limits, access control, logging |
  | Knowledge ingestion & publication | Malicious or unapproved knowledge affects outputs | Source validation, publish approvals, change history |
  | Chat with an external LLM | Data leakage or prompt manipulation | Data minimization, provider assessment, tool/authorization boundaries |
  | Future live Work Order write | Unauthorized or duplicate action | Human confirmation, permission checks, idempotency, result verification |

  These are suggested technical measures, not a technology list the
  law itself mandates. Art. 21 requires, among other things, risk
  management, supplier security, secure development, access control
  and business continuity.

- **Audit retention: correction.** ⛔ Art. 21 itself does not establish
  a general 5-year audit-retention obligation — this needs a separate
  legal or contractual basis, and final applicability must be confirmed
  by Telekom's legal/security function against applicable national law.
  Several NCT-AI use-case specs cite "NIS2 Art. 21, retention ≥5 years"
  as a design requirement; that framing should be read as a design
  choice made in response to a *plausible* compliance need, not as a
  quotation of what the article itself mandates. ⚠️ Whatever period is
  ultimately set must also respect GDPR's own storage-limitation
  principle (Art. 5(1)(e)) wherever the retained data includes personal
  data — a duration chosen only to satisfy one requirement can breach
  the other, so retention is agreed **per data category**, not as one
  number for "audit."
  **Audit retention policy will be agreed with Telekom's IT, security
  and legal functions per data category. No compliance assessment has
  been completed. Starting the pilot requires an approved scope and
  acceptance of the risks recorded here** — nothing in this section is
  legal advice, and it does not determine which national law applies
  to Telekom.
- **Durable audit is implemented and configured** —
  `Audit__Enabled=true` is set for AG-UI, Chat, Context, Memory,
  Runtime and Workflow (verified against the manifests, 2026-09-15).
  ⛔ What is **not** yet done: verifying that it is actually active in
  a running pilot, testing that events survive a restart, and agreeing
  coverage/tamper-protection/retention per scenario — no store enforces
  a retention *period* anywhere yet. Code and configuration existing is
  not proof of a working live deployment; this must be verified before
  a compliance sign-off, not assumed.
- Governed, read-only Oracle access (Oracle MCP) is designed for
  exactly this kind of requirement: SELECT-only, schema-allowlisted,
  every call audited.

- **GDPR applies on a different test than NIS2: personal-data
  processing, not "uses AI".** It is not safe to say "NCT-AI is about
  equipment, so GDPR doesn't apply" — a scenario can process no
  personal data in its technical calculation and still process it in
  the surrounding activity (who ran it, when, what they asked).
  Read-only access is not exempt: retrieval and display are processing
  too, and neither encryption nor replacing a name with an identifier
  is automatically anonymization.

  | NCT-AI scenario | GDPR relevance |
  |---|---|
  | AGG-01 computing a MUX expansion from ports/cards/capacity | Purely technical data is not automatically personal data |
  | The run logged together with the engineer who started it | Linking user, action and time is personal-data processing |
  | Oracle/NCTSite reads | Depends on the fields returned — names, contact details or subscriber identifiers need review |
  | Conversations and attachments | Can contain personal data about users or third parties |
  | Publishing a document as knowledge/OKF | Needs a check for authors, contacts and other people named inside the document |
  | Sending content to an LLM | If it contains personal data, that transmission and processing falls under GDPR |

  For each relevant flow, Telekom needs to define: purpose and legal
  basis for processing (not necessarily consent); strictly necessary
  fields, access rights and information to data subjects; retention
  period and deletion process, with applicable exceptions; and
  customer/provider roles with the required processing agreements.

  ⚠️ **GDPR does not mean "everything must stay in the EU."** Transfers
  outside the EEA are permitted under specific conditions; Telekom may
  still choose to impose a stricter contractual requirement than the
  law does. The EU-hosting measured in dev (above) is a fact about
  today's deployment, not evidence that GDPR requires it.

  **Bottom line:** this needs a per-flow personal-data mapping,
  confirmed with Telekom's Data Protection Officer (DPO) — not a
  blanket claim that NCT-AI is out of GDPR's scope because its subject
  matter is equipment.

- **Practical next step:** a per-scenario table — data → risks →
  controls → test evidence → accepting owner — reviewed with Telekom's
  legal and security functions before compliance sign-off.

**References:**
- NIS2 Directive (EU) 2022/2555, Art. 21 — <https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32022L2555>
- European Commission guidance on Art. 21(1) scope (¶7: covers all functions/services, not only critical systems) — <https://eur-lex.europa.eu/legal-content/EN/TXT/PDF/?uri=CELEX%3A52023XC0918%2801%29>
- GDPR (EU) 2016/679, Art. 5(1)(e) storage limitation — <https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32016R0679>
- European Commission — GDPR scope and definitions — <https://commission.europa.eu/law/law-topic/data-protection/information-business-and-organisations/application-gdpr_en>
- European Commission — GDPR principles — <https://commission.europa.eu/law/law-topic/data-protection/information-business-and-organisations/principles-gdpr_en>
- EDPB — international data transfers — <https://www.edpb.europa.eu/topics/international-transfers-and-international-cooperation_en>

## 6. What This Document Is Not

- Not a production sizing reference.
- Not a compliance certification, and not a full compliance review —
  it names the gaps found so far (audit retention, the GDPR personal-
  data mapping) rather than asserting compliance or claiming
  completeness.
- Not a hosting decision — Azure is the proven reference, not a
  requirement.

---

*Written against the current dev-cluster state (2026-09-17) and
Telekom's stated pilot scope. Re-derive figures before quoting them in
a customer-facing deliverable.*

</details>
