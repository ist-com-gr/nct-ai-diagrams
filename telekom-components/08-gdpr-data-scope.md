# NCT-AI / GDPR Data Scope

[Diagram index](index.md) | [Interactive HTML](08-gdpr-data-scope.html) | [JSON specification](https://github.com/ist-com-gr/NCT-AI/blob/31b7ec777623d9e3e4b179f82d5df9a16891fcc5/design/Prerequisites/diagram-lab/telekom-components/specs/08-gdpr-data-scope.json)

**Source:** [Telekom components overview](https://github.com/ist-com-gr/NCT-AI/blob/31b7ec777623d9e3e4b179f82d5df9a16891fcc5/design/Components/NCT-AI_Components_Overview_Telekom_2026-09-17.md), sections 6, 7. Snapshot: 2026-09-17.

**View:** Governance model. Classify technical data separately from engineer-linked activity, conversation content and provider disclosures.

## Text Diagram

```text
[inputs] Use-case data
  --- classify --> [classification] Personal-data check

[classification] Personal-data check
  --- if personal --> [purpose] Purpose and legal basis

[purpose] Purpose and legal basis
  --- constrain --> [processing] Controlled processing

[processing] Controlled processing
  - - if disclosed --> [providers] External processors

[providers] External processors
  --- align obligations --> [retention] Retention and rights
```

Arrows mean only the labeled relationship. They do not certify live traffic or a complete request trace.

## Components

| ID | Component | Context | Status / grouping |
|---|---|---|---|
| inputs | Use-case data | Equipment / user / chat / files | MAP EACH FLOW |
| classification | Personal-data check | Identified or identifiable person? | CLASSIFY |
| purpose | Purpose and legal basis | Necessary fields / access / notice | IF PERSONAL DATA |
| retention | Retention and rights | Category-specific deletion rules | AGREE WITH DPO |
| providers | External processors | LLM / hosting / transfer conditions | REVIEW CONTRACTS |
| processing | Controlled processing | Read / store / transmit / log | MINIMIZE + PROTECT |

## Relationships

| From -> to | Meaning | Scope |
|---|---|---|
| inputs -> classification | classify | Relationship as labeled |
| classification -> purpose | if personal | Relationship as labeled |
| purpose -> processing | constrain | Relationship as labeled |
| processing -> providers | if disclosed | Conditional / proposed |
| providers -> retention | align obligations | Relationship as labeled |

## Evidence and Limits

- Ports, cards and aggregate equipment capacity are not automatically personal data. A run linked to an identifiable engineer can still be personal-data processing even when its technical calculation is not.
- Check the actual Oracle fields, chat content, attachments, authors/contact details in knowledge sources, prompts and user-linked logs. Do not infer that any dataset is personal solely because it is stored in Oracle.
- The personal-data branch needs a defined purpose and lawful basis, necessary fields, access controls, notices, appropriate retention and handling of individual rights. Consent is not automatically the required basis.
- Internal processing needs the same applicable safeguards even when no external model provider is used. The conditional provider branch adds processor agreements and any applicable international-transfer safeguards.
- Read-only retrieval is processing. Encryption or pseudonymization does not automatically make data anonymous. Truly non-personal data does not become personal merely because an AI model uses it.
- GDPR is not a blanket EU-only hosting rule. Telekom may impose stricter contractual boundaries. Review each flow with the DPO; this diagram is not legal advice or a compliance certificate.

## Source Snapshot

The following excerpt is attributed to the source document, not newly verified live evidence. Any unresolved claims are qualified above.

<details>
<summary>Source sections 6, 7</summary>

## 6. Data Residency &amp; Compliance

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

  | NCT-AI scenario                       | Risk                                                     | Indicative controls                                                     |
  | ------------------------------------- | -------------------------------------------------------- | ----------------------------------------------------------------------- |
  | PoTP/MUX recommendation (AGG-01)      | Corrupted data or rules drive a dangerous recommendation | Version-controlled config, data provenance, testing, decision logging   |
  | NCTSite read via Oracle MCP           | Unauthorized access or leakage of network data           | Least privilege, query limits, access control, logging                  |
  | Knowledge ingestion &amp; publication | Malicious or unapproved knowledge affects outputs        | Source validation, publish approvals, change history                    |
  | Chat with an external LLM             | Data leakage or prompt manipulation                      | Data minimization, provider assessment, tool/authorization boundaries   |
  | Future live Work Order write          | Unauthorized or duplicate action                         | Human confirmation, permission checks, idempotency, result verification |


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

  | NCT-AI scenario                                            | GDPR relevance                                                                                |
  | ---------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
  | AGG-01 computing a MUX expansion from ports/cards/capacity | Purely technical data is not automatically personal data                                      |
  | The run logged together with the engineer who started it   | Linking user, action and time is personal-data processing                                     |
  | Oracle/NCTSite reads                                       | Depends on the fields returned — names, contact details or subscriber identifiers need review |
  | Conversations and attachments                              | Can contain personal data about users or third parties                                        |
  | Publishing a document as knowledge/OKF                     | Needs a check for authors, contacts and other people named inside the document                |
  | Sending content to an LLM                                  | If it contains personal data, that transmission and processing falls under GDPR               |


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

- NIS2 Directive (EU) 2022/2555, Art. 21 — [https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32022L2555](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32022L2555)
- European Commission guidance on Art. 21(1) scope (¶7: covers all functions/services, not only critical systems) — [https://eur-lex.europa.eu/legal-content/EN/TXT/PDF/?uri=CELEX%3A52023XC0918%2801%29](https://eur-lex.europa.eu/legal-content/EN/TXT/PDF/?uri=CELEX%3A52023XC0918%2801%29)
- GDPR (EU) 2016/679, Art. 5(1)(e) storage limitation — [https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32016R0679](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32016R0679)
- European Commission — GDPR scope and definitions — [https://commission.europa.eu/law/law-topic/data-protection/information-business-and-organisations/application-gdpr\_en](https://commission.europa.eu/law/law-topic/data-protection/information-business-and-organisations/application-gdpr_en)
- European Commission — GDPR principles — [https://commission.europa.eu/law/law-topic/data-protection/information-business-and-organisations/principles-gdpr\_en](https://commission.europa.eu/law/law-topic/data-protection/information-business-and-organisations/principles-gdpr_en)
- EDPB — international data transfers — [https://www.edpb.europa.eu/topics/international-transfers-and-international-cooperation\_en](https://www.edpb.europa.eu/topics/international-transfers-and-international-cooperation_en)

## 7. What This Document Is Not

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
