# NCT-AI / Installation Decisions

[Diagram index](index.md) | [Interactive HTML](06-installation-decisions.html) | [JSON specification](https://github.com/ist-com-gr/NCT-AI/blob/889128d85af7dcf478c9651e89df05d1288ec825/design/Prerequisites/diagram-lab/telekom-components/specs/06-installation-decisions.json)

**Source:** [Telekom components overview](https://github.com/ist-com-gr/NCT-AI/blob/889128d85af7dcf478c9651e89df05d1288ec825/design/Components/NCT-AI_Components_Overview_Telekom_2026-09-17.md), sections 4, 6, 7. Snapshot: 2026-09-17.

**View:** Decision dependencies. Hosting, identity, model processing location and artifact storage must be qualified before pilot acceptance.

## Text Diagram

```text
[hosting] Pilot environment
  --- configure --> [dependencies] Dependency contracts

[dependencies] Dependency contracts
  --- test --> [acceptance] Pilot acceptance

[providers] Identity and models
  - - qualify adapters --> [hosting] Pilot environment

[artifacts] Knowledge files
  - - resolve storage --> [dependencies] Dependency contracts

[residency] Data location
  - - verify boundary --> [acceptance] Pilot acceptance
```

Arrows mean only the labeled relationship. They do not certify live traffic or a complete request trace.

## Components

| ID | Component | Context | Status / grouping |
|---|---|---|---|
| hosting | Pilot environment | Kubernetes / Istio / telemetry | HOSTING UNDECIDED |
| dependencies | Dependency contracts | PostgreSQL / Redis / Temporal | QUALIFY |
| acceptance | Pilot acceptance | Evidence and named risk owners | NOT CERTIFIED |
| providers | Identity and models | Azure / Entra reference adapters | PORTABILITY CHECK |
| artifacts | Knowledge files | Artifacts / attachments / recovery | STORAGE GAP |
| residency | Data location | Storage AND model processing | VERIFY PER FLOW |

## Relationships

| From -> to | Meaning | Scope |
|---|---|---|
| hosting -> dependencies | configure | Relationship as labeled |
| dependencies -> acceptance | test | Relationship as labeled |
| providers -> hosting | qualify adapters | Conditional / proposed |
| artifacts -> dependencies | resolve storage | Conditional / proposed |
| residency -> acceptance | verify boundary | Conditional / proposed |

## Evidence and Limits

- The source prerequisites include Kubernetes/Istio, PostgreSQL with vector and AGE, Redis, Temporal, Oracle connectivity, a model provider, OIDC identity and OpenTelemetry backends.
- REVIEW CAVEAT: the source still asserts infrastructure neutrality. Azure/Entra is the reference; a replacement identity or model provider needs adapter and integration validation, not just a new hostname.
- REVIEW CAVEAT: durable artifact/attachment storage is omitted from the source's installation list. The prior review identifies current Blob/file-store dependencies. Agree an alternative if object storage is excluded; do not silently assume PostgreSQL stores every file.
- REVIEW CAVEAT: the source still describes EU residency as proven. EU-hosted databases alone do not prove the processing location of embeddings, chat or verification models. GlobalStandard settings previously identified in the repository require deployment-specific checks.
- Dashed relationships are unresolved qualification dependencies. This view does not change the source document, re-audit providers or certify a running deployment.

## Supporting References

- Prior technical review (Greek) (`../../../../docs/reviews/2026-09-17-telekom-components-overview-review.gr.md`)
- Model deployment configuration (`../../../../deploy/azure/bicep/openai.bicep#L105`)
- [Artifact storage registration](https://github.com/ist-com-gr/NCT-AI/blob/889128d85af7dcf478c9651e89df05d1288ec825/src/NCT.AI.Knowledge.Persistence/KnowledgeArtifactStorageRegistration.cs#L41)
- [Identity group verification](https://github.com/ist-com-gr/NCT-AI/blob/889128d85af7dcf478c9651e89df05d1288ec825/src/NCT.AI.AgUi.Web/Authentication/GraphGroupMembershipVerifier.cs#L60)

## Source Snapshot

The following excerpt is attributed to the source document, not newly verified live evidence. Any unresolved claims are qualified above.

<details>
<summary>Source sections 4, 6, 7</summary>

## 4. Installation — What Telekom's Environment Needs

Same requirement list as the DEV document, unchanged by scale:

- Kubernetes cluster (any distribution) with Istio service mesh
- PostgreSQL with `vector` extension and Apache AGE enabled
- Redis
- Temporal
- At least one model provider (Azure OpenAI or equivalent) behind the
Model Router — a provider-neutral routing layer, so a deployment is
never locked to one provider. ⚠️ Azure OpenAI is the only provider
implemented today; the router's abstraction supports adding others.
- NCTSITE Oracle DB reachability
- OpenTelemetry Collector + a metrics/trace/log backend
- An OIDC identity provider (Entra ID or equivalent)

**Hosting is not yet decided.** The reference implementation proven in
dev runs on Azure (West Europe for the Azure-hosted
pieces, Oracle ADB in Frankfurt) — every proven-working configuration
in this project is on that stack. The components themselves are
infrastructure-neutral; nothing above requires Azure specifically.

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
