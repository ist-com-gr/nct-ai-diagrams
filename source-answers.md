# IT Questionnaire Answer Snapshot

Version: 0.49-draft. These are the complete answers already embedded in the Diagram Lab, not the missing parent document or its appendices.

[Diagrams](index.html)

## Q1 - Current State vs Target State slide, with new systems visible

**Answer.** Yes. The presentation should distinguish the existing NCTSite/Oracle/external-systems
landscape from the eighteen new components NCT-AI introduces: AG-UI, API/BFF gateway, Agent Runtime,
Model Router, Knowledge Compiler, the Approved OKF Knowledge Bundle repository, retrieval services,
PostgreSQL/pgvector, graph knowledge via Apache AGE, the deterministic Rule Engine, Temporal workflow
orchestration, the MCP/Tool Gateway, the Oracle read-only MCP layer, the Policy/Guardrail service, the
Evidence/Audit/Trace subsystem, the model provider (Azure OpenAI or local), Azure Managed Redis, and
CI/CD/release controls. NCTSite and Oracle remain the authoritative systems of record; NCT-AI adds a
governed intelligence and execution layer around them rather than replacing them
(`nct-ai-architecture-design.md` §1–2, `nct-ai-azure-deployment.md` §1 carry the same distinction with
diagrams). The Azure implementation of this target is no longer only a paper architecture: an AKS
deployment of the NCT-AI service set has been executed and observed running.

The additional response explicitly retains NCTSite WebForms/MVC, Oracle, the SAP feed, RP/TR modules
and Entra ID. External systems remain in place; retaining them does not eliminate interface work.
The requested slide should show existing flows separately from new interfaces and phase-gated writes
(Q19–Q20). This questionnaire update does not itself deliver the revised presentation slides.

## Q2 - Azure OpenAI & Local/On-Premises AI slide

**Answer.** Every governed model call passes through the Models plane and Model Router; provider SDKs
are isolated inside provider adapters such as `NCT.AI.Models.AzureOpenAI`. The router and core
contracts are provider-neutral. The same application-facing APIs can support Azure-managed, hybrid
Azure/on-premises and fully on-premises inference without coupling business logic to a provider SDK.

**Current state.** Azure OpenAI is connected in development. The local-provider interface is tested,
but a Production local-inference backend is not yet evidenced. Assembly-boundary tests verify the SDK
separation (Appendix B1). Provider independence does not itself enforce data residency: chat and
embedding deployments have different processing scopes, as explained in Q15.

**Target / remaining gap.** Azure-managed inference avoids customer-managed GPUs; local inference
offers greater control of the data path but requires GPU investment, serving operations and capacity
planning. Hybrid adds two operating environments. Each option needs capability, security, quality and
regression validation, including private connectivity and permitted egress. The source's hybrid
estimate of approximately 90% of Azure runtime cost and approximately EUR 200K GPU CAPEX are
unvalidated planning assumptions, not quotations or demonstrated savings.

## Q3 - UAT and Production sizing/capacity tables

**Answer.** The tables below are the **recommended planning baseline**, not final procurement sizing — final values
need validation from performance/load testing against the real AGG-01 and subsequent use cases.
Planning assumptions: ~1,000 registered users, ~100 peak active users, ~20 concurrent LLM generations,
typical context ~32K tokens, multiple agent/model calls per complex request, embeddings enabled,
production HA required. Where Azure OpenAI is used, model-inference GPU capacity sits outside the
NCT-AI AKS CPU/RAM sizing below.

**UAT baseline**

| Component | CPU | RAM | Storage | Growth / notes |
|---|---:|---:|---:|---|
| AKS system + application workloads | 24–32 vCPU baseline | 96–128 GB | 200–300 GB ephemeral/platform | scale with test concurrency |
| PostgreSQL / pgvector / AGE | 4–8 vCPU | 32–64 GB | 256–512 GB | plan 20–30% annual logical growth initially |
| Temporal persistence/workers | shared or dedicated | 8–16 GB incremental if dedicated | 100–200 GB history budget | retention must be configured |
| Redis | managed service | 2–6 GB cache | non-authoritative | cache only |
| Knowledge/object artifacts | n/a | n/a | 250–500 GB | manuals, OKF, indexes, test sets |
| Logs / traces | n/a | n/a | policy-driven | shorter retention in UAT |

Recommended UAT topology: a minimum 3-node resilient cluster, separate system/application node pools
where practical, Horizontal Pod Autoscaling, and non-production but production-like network/security
controls.

**Production baseline**

| Component | CPU | RAM | Storage | Growth / notes |
|---|---:|---:|---:|---|
| AKS application baseline | 48–64 vCPU | 192–256 GB | 300–500 GB platform | autoscale to 96–128 vCPU / 384–512 GB at peak |
| PostgreSQL / pgvector / AGE | 8–16 vCPU | 64–128 GB | 0.5–1 TB initial | measured per bundle/conversation/audit policy |
| Temporal services/workers | horizontally scaled | workload-dependent | HA persistence | multiple replicas, per-workload queues |
| Azure Managed Redis | managed HA tier | by cache profile | non-authoritative | sized from session/cache hit rate |
| Knowledge/object storage | n/a | n/a | 0.5–1 TB initial | versioned manuals, bundles, artifacts |
| Audit/log/trace | n/a | n/a | potentially 1+ TB/year | depends heavily on payload retention policy |

These figures are deliberately conservative planning ranges, to be validated against request rate,
document corpus size, embedding chunk count, conversation retention, audit payload size, Temporal
history retention, concurrent tool calls and actual rule-execution load. On-prem model inference needs
a separate GPU capacity table, driven by model choice, precision/quantisation, context length,
concurrency and required tokens/sec — it cannot be derived from the AKS figures alone.

Provider capacity also needs model- and environment-specific RPM/TPM quotas (requests/tokens per
minute), request arrival rate, input/output token distributions, calls per workflow, retries, and
p50/p95/p99 latency under an agreed workload. Twenty concurrent generations and a 32K context alone
cannot establish throughput or cost. UAT must record quota throttling and queueing as well as AKS
resource use; no validated Production quota or capacity guarantee is asserted here.

**Target / remaining gap.** Validate this single recommended baseline in UAT before procurement.
The alternative 2028+ proposal is retained only in Appendix A as a scenario under evaluation, not a
second recommendation or an approved capacity commitment.

## Q4 - Relationship between the Approved OKF Knowledge Bundle and executable deterministic rules

**Answer.** **C# executes the current AGG-01 checks; OKF organises the approved knowledge that explains
and supports them.** This describes the current implementation, not a requirement that every future
business rule must remain compiled C#.

**Current state: BUILT.** The knowledge compiler validates and packages versioned OKF documents,
vendor constraints, equipment information and source references. It does not generate executable C#
rule bodies. Changes to current executable logic require code review, regression tests and an
application release, not just approval of a revised manual. Source-object references connect results
to supporting knowledge; they are not executable rule compilation.

C# does not require OKF to execute rules. The governed AGG-01 path deliberately requires approved
knowledge as an application control, with these separate responsibilities:

| Concern | Responsible component |
|---|---|
| Current equipment facts | NCTSite/Oracle and their governed interfaces |
| Executable checks and engineering decision | C# rules |
| Requirement, vendor clause and supporting source | OKF objects and references |
| Approved version, integrity and execution pin | Registry and validation code operating on the bundle |

Approval and integrity checks are enforced by application code, not by the format alone. A source ID
or matching hash does not prove that C# agrees with the documented requirement (Q6). The justification
for OKF is governance and traceability, not automatic rule generation or a need to send the full OKF
text to the explanation model. Simpler alternatives are in Q28; implementation evidence is in Appendix B2.

**Target / remaining gap: DESIGNED for general declarative execution.** The rules-as-data plan supports
a selective, controlled declarative layer for frequently changing vendor/business conditions where
justified. This needs machine-evaluable predicates, SME validation, versioning, approval, regression
coverage and compatible release/rollback controls. Prose does not become executable by packaging it
as OKF. Procedural checks can remain C#; safety invariants, authorisation and write restrictions must
remain enforced by code/policy independently of declarative content or LLM output. The plan is not
evidence that all current rules already run as data.

## Q5 - New/revised vendor manual → approved Production knowledge/OKF

**Answer.** The target lifecycle is versioned: register the source and revision, retain an immutable
source artifact, extract and normalise content, propose knowledge changes, analyse their impact, build
a candidate OKF bundle, validate schema and references, build retrieval projections, run Golden Set
regression, obtain SME review and an approval record, validate in UAT, then promote to Production.
AI-assisted extraction produces proposals, never approvals. Production policy requires approved
sources before activation in the trusted knowledge set.

**Partially BUILT:** OKF authoring and knowledge compilation, AGG-01 Golden Set regression, and the
authority-release mechanism that binds an execution to an approved knowledge version. An Active release
is recorded in the development database (`CONFIG.md`, "PoTP authoritative knowledge pinning").
SME review remains manual and out of band; this is not evidence of a completed automated
DEV-to-UAT-to-Production release.

Knowledge compilation here means validated knowledge packaging and projections, not generation of
executable C# rules. When a manual revision changes implemented rule logic, a separate code change,
regression suite and approved application release are required alongside the knowledge release (Q4).

The additional response proposes separate Knowledge Author and Policy Reviewer responsibilities;
this separation of duties should be an acceptance requirement, not inferred from role names alone.
Its XDOM-01 OCR/manual-ingestion sequence is a target integration, not evidence of a completed
PDF-to-production pipeline. The suggested 1–3 working days for a familiar equipment family, or a week
or more for a new structure, is an indicative estimate requiring SME availability, extraction quality,
test coverage and any necessary C# change to be assessed. It is not a delivery SLA.

## Q6 - Automated / AI-assisted / manual steps, and impact detection

**Answer.** The automation boundary must distinguish available tooling from the complete target flow.

| Step | Current position |
|---|---|
| OKF validation/packaging and AGG-01 Golden Set regression | **BUILT** tooling; see Q4–Q5 |
| Source registration, engineering interpretation, SME approval and release authorisation | Manual governance responsibilities |
| Extraction of proposed knowledge, semantic comparison, contradiction analysis and proposed test cases | AI-assisted authoring activities, not an autonomous approved ingestion service |
| Complete source-revision detection, impact analysis, projection rebuild and cross-environment promotion | **DESIGNED** as an end-to-end automated flow; individual components do not prove the whole flow |
| Executable rule changes | C# implementation and release today; general declarative rule compilation remains **DESIGNED** |
| Artifact signing and signature verification in a release | Mechanisms exist; successful release execution is not yet evidenced (Q12, Q26) |

The proposed lineage graph links source clause, OKF object, executable rule, agent, bundle, Golden test
case and retrieval projection in both directions. Automatically deriving a complete Impact Set from a
manual revision remains **DESIGNED**. Today an author uses bundle cross-references and code/test
dependencies to assess the impact. An AI-produced proposal never replaces engineering approval.

The main consistency risk is semantic drift: an OKF requirement can change without the C# rule
changing, or vice versa. Proposed controls are an explicit source-clause → OKF object → C# rule →
test mapping; SME-approved expected outcomes in shared acceptance tests; classification of each
revision as documentation-only or behaviour-changing; and coordinated knowledge/code review and
release for the latter. IDs, hashes and graph links support that review but do not establish semantic
equivalence. Full automatic knowledge-to-code equivalence checking is not claimed.

LLM-assisted comparison of manuals, extraction of candidate requirements and identification of
ambiguities are useful development directions. Each needs a separately implemented and evaluated
scenario path; none is an implicit capability of today's AGG-01 explanation call.

## Q7 - "Training/improvement" process and Human-in-the-Loop

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

## Q8 - Rejection clustering, Golden Set expansion, and mandatory SME approval

**Answer.** Engineer feedback should be captured as a structured feedback event, linked to the
recommendation, evidence and relevant versions. Rejection clustering would then group similar failures
to identify systemic issues. This is the intended improvement process, not a claim that a persistent,
end-to-end feedback capture and analytics pipeline is operational.

**Current state.** Golden Set regression is **BUILT** for AGG-01. Individual approval/rejection audit
records do not establish a general feedback entity/API or a complete feedback loop. End-to-end
feedback tooling and rejection clustering remain **DESIGNED** (Q7; Appendix B2).

**Target / remaining gap.** A confirmed failure should become a reproducible regression case:
reproduce, determine the correct expected outcome, extend the Golden Set, fix the rule/knowledge/prompt
and prove that the regression passes. Feedback is triage evidence, never a direct conversion into
Production rules. Analytics and draft candidates do not themselves authorise Production activation.
Any change to Production knowledge or rules that can alter an engineering outcome requires
SME/Knowledge Approver approval before activation. Q5 governs knowledge releases; executable C#
changes also require the separate code-review, regression and application-release controls in Q4/Q12.

## Q9 - Promotion of model versions, rules, knowledge and embeddings between UAT/Production, and "learning" transfer

**Answer.** The target promotion process is artifact-based and immutable, across application images,
agent definitions, prompt templates, policy versions, executable rule code, approved OKF bundles,
embedding-model configuration, vector-index manifests and model-routing configuration: build once,
promote the same approved artifact
through DEV → candidate → UAT (regression, security tests, SME acceptance) → an approved release
manifest → Production, never rebuilding a "similar" bundle from source once UAT has validated it.
Embeddings may be physically regenerated per environment but must derive from the same approved
source bundle, chunking version, embedding-model version and projection manifest, ideally built
side-by-side and switched via an alias after validation. Model changes are configuration-controlled,
but provider-side upgrade policy must be governed separately (Q21; Appendix B4). There is no direct,
automatic "Production learns and updates itself" path — Production telemetry/feedback can inform a
UAT/DEV candidate, but only through the governed regression-and-approval lifecycle in Q7–Q8, in one
direction, human-gated at release approval. **BUILT** for knowledge authority activation and pinning in
development (Q5); this is not proof of cross-environment promotion. End-to-end Production promotion,
including model configuration and executable rule/application releases, remains **DESIGNED** and
unverified. Current C# rules are promoted with the application, not by an independent OKF alias.

An equal source-bundle hash does not by itself prove equal embedding bytes. Re-projection also depends
on preprocessing, chunking, model/version and service behaviour. Record the projection manifest and
validate retrieval quality; retain exact artifacts where byte-for-byte reproducibility is required.

## Q10 - Intent → Agent → allowed Knowledge Bundle mapping; ambiguous/no-match requests

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

## Q11 - Dedicated GUI vs embedding into the existing NCTSite UI

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

## Q12 - Target CI/CD and deployment model; manual steps; release launch process

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

## Q13 - Protection against prompt injection and malicious retrieved content

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

## Q14 - Service-to-service authentication and encryption in Production

**Answer.** The Production baseline requires private networking, per-service identity, explicit
service-to-service authorisation, STRICT mutual TLS within the mesh, network restrictions and TLS
to external dependencies. Workload/managed identity is preferred to shared or stored credentials.

**Current state.** The repository records the AKS service decomposition and an Istio implementation
foundation. This does not establish enforcement throughout a continuously available Production
topology. Container counts or readiness alone are not evidence of a mesh security control.

**Target / remaining gap.** Evidence must identify the deployed `istio-proxy` containers in the
sidecar-based topology, effective `PeerAuthentication` in `STRICT` mode, explicit
`AuthorizationPolicy` allow rules, NetworkPolicies, Workload Identity bindings, private endpoints,
restricted egress and certificate/token rotation. Positive and negative connectivity tests must
prove permitted traffic works and unauthorised service connections are refused, including relevant
external dependencies such as PostgreSQL, Oracle, Redis and model/API endpoints.

SPIFFE/SPIRE attestation and Entra Workload Identity bindings are separate mechanisms. Neither a
sidecar nor an Azure identity binding alone proves attestation, STRICT mTLS or coverage of all
outbound traffic. Each claim needs deployed configuration and test evidence for its environment.

## Q15 - Data processed by NCT-AI, what reaches Azure OpenAI, and minimisation/retention/deletion policy

**Answer.** NCT-AI processes user requests, identity/session metadata, selected engineering facts,
approved knowledge and retrieval projections, workflow state, model requests/responses, and audit
references. Oracle remains authoritative for the operational facts. The intended model payload is a
user question plus selected knowledge snippets, curated facts and rule outcomes, not complete Oracle
tables, credentials or unrelated user/history data. Field allowlists, typed tools and context budgets
support minimisation; classification and masking coverage must be validated per use case before
Production.

Payloads differ by path: model-backed chat can send the user prompt, selected history and retrieved
context; embedding projection sends selected knowledge chunks when that projection is generated or
rebuilt, not necessarily only once. The PoTP explanation path sends decision/reason information and an
element identifier, not the full Oracle facts or full OKF bundle. Its deterministic decision needs no
model call. The new response's free-text routing and approval-reply classification examples must not be
treated as universal implemented model calls; the current intent router is rule-based (Q10).

The plan verifier receives selected prior-step artifacts, so those artifacts need their own
classification and minimisation review. Its lack of tools does not mean no content is sent to the
model. Conversely, the AGG-01 explanation payload does not justify sending a complete OKF corpus.

Three separate boundaries govern retention and processing:

| Boundary | Current position and requirement |
|---|---|
| NCT-AI storage | The ledger library defaults to `CaptureContent=None`, but `dev/local/start.sh` explicitly defaults to `BoundedMetadata`. Effective configuration must be checked per environment. Outside Development, enabling that capture requires a bounded content-retention setting; this startup validation does not prove deletion across all stores. |
| Provider retention | Microsoft may retain selected prompts/completions for abuse-monitoring human review. Modified monitoring requires separate approval; none is assumed here. Local capture settings do not control it. |
| Processing geography | Repository Bicep defaults specify chat `gpt-5.4-mini` as `DataZoneStandard` and embeddings `text-embedding-3-small` as `GlobalStandard`. This is configuration evidence, not a fresh inspection of live deployments. |

Microsoft states that customer content is not used to train foundation models without permission.
EU DataZone processing stays within the EU; Global processing can occur elsewhere. Storage geography
is distinct, and a private endpoint does not determine inference location.
[Microsoft data privacy and processing geography](https://learn.microsoft.com/en-us/azure/foundry/responsible-ai/openai/data-privacy).

For an EU-only processing requirement, the embeddings deployment must be changed to a supported
compliant option or receive explicit approval for the exception. Verify each live deployment's SKU,
model/version, retention features and abuse-monitoring status before approval.

Proposed **NCT-AI** retention periods, subject to Telekom approval: conversations 30 days UAT /
90 days Production; diagnostic prompt/completion copies 7–30 days UAT / 30 days or disabled in
Production; feedback 12–24 months, de-identified where possible; embeddings until source retirement;
audit/security metadata 90–180 days UAT / 12–24+ months Production. These are policy proposals, not
implemented retention guarantees or provider retention periods. Deletion procedures must cover
conversations, derived projections, workflow history and backups according to the approved policy,
with verification of expiry and deletion. Q25 and Q29 distinguish usage measurements from cost estimates.

The additional response proposes 30-day prompts, 90-day active conversations followed by archive,
two-year invocation metadata and at least five-year audit retention. These are alternative policy
inputs, not approved replacements for the ranges above. Compliance must resolve them into one schedule
per data category, including archive expiry, legal holds and backups. The attribution of five years to
NIS2 Article 21 is not accepted as verified legal justification; the applicable legal basis and duration
must be confirmed. Nor is a provider "no data logging" opt-out assumed enabled without approval evidence.

## Q16 - Technical enforcement that the Oracle read path is read-only, and separation from the Work Order write path

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

## Q17 - End-to-end audit trail and root-cause debugging of wrong recommendations

**Answer.** **Mandatory Production/UAT requirement, not yet complete at every hop.** The target audit
record links user/role/session, execution and trace IDs, intent/agent/policy versions, fact and tool
references, pinned knowledge version, executable rule version/results, prompt/model versions, usage,
evidence, final recommendation and feedback. A durable queryable trail exists in `NCT.AI.Chat`
and the AG-UI Trace view exposes correlation; full cross-service coverage and user attribution still
need verification (Q25).

Auditability is not identical to exact replay. Identifiers and hashes establish correlation and
integrity but cannot reconstruct deleted facts or model content. Reading Oracle again can return
different facts. Deterministic reproduction requires retained input snapshots or immutable fact
artifacts, the originally pinned knowledge, and compatible rule/application versions. Model inputs and
outputs can only be inspected where capture and retention policy permit; calling the same model again
does not guarantee the same answer. Temporal history replays recorded workflow decisions, not a
guarantee that a newly executed external read or inference reproduces the original result.

Investigation therefore first establishes which evidence remains available, inspects the recorded
versions and outcomes, distinguishes original facts from fresh observations, classifies the cause and
adds a regression case. Missing or expired evidence must be reported as a reproducibility limitation,
not reconstructed from hashes or silently replaced with current data.

The additional response's statement that no environment has any durable audit storage is too broad:
chat persistence and model invocation records must be distinguished from incomplete platform-wide
audit coverage. A metadata-only ledger also cannot show the original model answer without an approved,
retained content artifact.

Record the distinction between rule decision, knowledge approval, model-authored explanation,
verifier verdict and engineer approval. An approved knowledge pin establishes which source version was
used, not that the C# rule faithfully implements its meaning; that needs the consistency checks in Q6.

## Q18 - High Availability and Disaster Recovery

**Answer.** Production HA and DR are designed separately from the current development/UAT operating
mode: the cluster already exists and NCT-AI workloads have been deployed and observed running, and being
stopped when idle in the current cost-optimised non-production mode is a cost-management decision, not a
failure of HA design — Production needs a different operating posture of continuously running capacity,
multi-replica workloads, zonal resilience where supported, monitored failover and tested DR. AKS:
production Standard/Premium tier, multiple availability zones where the region supports them, at least
3 nodes, multiple pod replicas, topology spread/anti-affinity, PodDisruptionBudgets, HPA,
readiness/liveness/startup probes, rolling deployment, zone-aware storage — the next step is not
"deploy to AKS" (already demonstrated in namespace `nct-ai`) but proving replica distribution, zone
behaviour, failover and recovery under the final Production topology. PostgreSQL/pgvector/AGE: Azure
Database for PostgreSQL Flexible Server with zone-redundant HA, PITR/backups, and geo-redundant backup
or cross-region read replica per DR requirements. Microsoft's HA figures concern the database:
RPO 0 for committed transactions and typical failover of 60–120 seconds, which can take longer under
load. They are not a hard 120-second bound or the application's end-to-end RTO; reconnects, workers,
queues and dependent services need separate recovery tests, as does regional DR.
[PostgreSQL HA documentation](https://learn.microsoft.com/en-us/azure/postgresql/high-availability/concepts-high-availability).
Temporal: self-hosted with multiple
Frontend/History/Matching replicas and HA PostgreSQL persistence, or Temporal Cloud as the managed
alternative. Redis: Azure Managed Redis, holding no authoritative data that cannot be reconstructed.
Model provider: primary plus controlled fallback deployment with timeout/circuit-breaker for Azure, or
N+1 replicated capacity with a load balancer for a local option. DR: a primary-region topology (AKS,
PostgreSQL HA, Managed Redis, storage, model endpoint) replicating configuration/data to a secondary
region on warm standby; an initial discussion target of near-zero RPO for critical
configuration/knowledge artifacts (already replicated/source-controlled), a customer-selected RPO for
operational PostgreSQL, and an RTO target in hours rather than days for platform recovery. Final RTO/RPO
require Telekom Business Continuity approval.

The new response supplies three options for discussion: backup/restore at RPO 24h and RTO 48h;
warm standby at RPO 1h and RTO 4h; and active/active aiming at RPO 0 and RTO under 15 minutes.
These are untested business targets, not properties obtained merely by choosing those topologies.
In particular, RPO 0 across regions requires a demonstrated replication/consistency design for every
authoritative store; zonal database HA does not establish it. Select a tier and test the complete
recovery path, including knowledge, Temporal state, identities, networking and model availability.

## Q19 - New NCTSite APIs/views/interfaces required for integration

**Answer.** AGG-01 already has two operational read integration paths in code: curated Oracle views
through the Oracle MCP server, and typed NCTSite REST reads through the PoTP MCP/API path. Knowledge
retrieval provides supporting approved content; it is not a third live read of NCTSite.

The existing PoTP REST client contracts use base path `api/v1/potp-mux`:

| Method | Relative path | Purpose |
|---|---|---|
| GET | `components/{id}/creation-eligibility` | Creation eligibility |
| GET | `components/{id}/shelf-context` | Shelf context |
| GET | `components/{id}/installed-equipment` | Installed equipment |
| GET | `cards/{id}/interfaces` | Card interfaces |
| GET | `chassis/{id}/slots` | Chassis slots |
| GET | `next-code` | Next-code lookup |
| POST | `work-orders` | Separate approval/policy-gated Work Order command; currently DryRun only (Q16) |

**Partially BUILT:** these client contracts and the curated Oracle integration. The existence of a
client or mocked contract test does not prove every endpoint is authorised and operational in
Production. The Work Order command is not part of the read-only Oracle connection.

Additional per-use-case curated views, entity-context/deep-link integration, user/role/scope mapping,
optional change events and contract-version negotiation require agreement with the NCTSite owner.
These broader integrations remain **DESIGNED** unless separately evidenced. Keep the stable
view/API contract independent of NCTSite's internal table layout.

**Proposed delivery scope:** Phase 1 is an advisory-only AGG-01 pilot. The source's Phase 2 write
proposal is not approved scope or permission to execute writes. The current decision remains
DryRun-only (Q16); changing it requires a separate owner decision and acceptance, regardless of phase.
Enforce this restriction through code/deployment policy and permissions, not just UI.
The new response also requests approval before sensitive reads and auditable readback: agree the
dataset classification, approver and enforcement point; do not infer these controls from a view alone.
Its estimates of 10–15 views per scenario and approximately 200 overall are not a verified inventory.
Count reusable data contracts first. The proposed `NCTAI_WO_WRITE` account must not be provisioned
under the current DryRun-only decision; its name is not evidence of an API identity or effective grants.

## Q20 - External systems/interfaces required per use case and phase (Atoll, mcSON, SAP, Ariadne)

**Answer.** Requirements are partly known from the scenarios; implementation status and interface
approval are separate questions. Absence of a direct NCT-AI connector does not make an existing
business-system dependency unknown.

| Scenario / system | Specified requirement or existing flow | NCT-AI implementation / outstanding decision |
|---|---|---|
| RAN-03 / Atoll | Planning outputs are a source; the scenario describes an existing, partly manual exchange with NCTSite | Direct automated integration is not evidenced; agree supported API/export, direction, cadence and owner with the SME |
| RAN-05 / mcSON | Licensing-table workflow includes NCTSite–mcSON exchange | Confirm the existing interface and whether NCT-AI needs direct access or consumes NCTSite data |
| RAN-02 and AGG-04 / SAP | Existing nightly SAP-to-NCTSite warehouse/material feed | Prefer the existing governed NCTSite data where adequate; a new direct SAP integration is not automatically required. Validate freshness and fields |
| AGG-01 / SAP-derived availability | Deterministic rules use availability facts | This does not establish a direct SAP connector; verify provenance and freshness through the supplied NCTSite facts |
| Ariadne | The additional response proposes exclusion from the current phase | Record as proposed out of scope, subject to customer acceptance; no confirmed connector requirement |
| RAN-05/RAN-06 / mcSON; RAN-03/RAN-05 / Atoll | The additional response proposes Phase 2 read/write and new-site exchanges | Confirm each scenario mapping, direction and existing/new contract; do not infer ready REST endpoints from this proposal |
| RAN-03/RAN-05 / eSurvey | The additional response lists site creation and licensing-table drafting | Proposed/in discussion; confirm owner, supported contract and delivery phase |
| RAN-05 / Cronoss | The additional response proposes an event-triggered Work Order/licensing-table push | Proposed Phase 2 dependency, not a deployed NCT-AI integration |
| AGG-06/MW-04 / OSS/NMS | The additional response proposes scheduled snapshots | Proposed Phase 2; agree exporter, freshness and ownership, without assuming live network control |

Sources: `submodules/NCT-AI/docs/specs/ran-03-new-site-requirements.md`,
`ran-05-licensing-table-requirements.md`, `ran-02-material-planning-requirements.md` and
`agg-04-warehouse-requirements.md` in the same directory. Scenario requirements are not evidence of
completed SME approval or a deployed integration.

The formal Use Case × Interface matrix should record the requirement, existing flow, proposed new
interface, read/write scope, owner, approval, implementation and deployment status, and delivery phase.
For any new connection, use the system's supported API/export/integration layer, register the tool,
and apply policy, timeout/retry, schema/version and audit controls. Do not introduce direct database
dependencies or an extra connector merely because a system appears in a scenario.

## Q21 - Validation/regression before changing the LLM or embedding model in Production

**Answer.** A model change is a controlled software-dependency change: register the candidate
model/version → deterministic compatibility tests → offline Golden Set → quality comparison against
the current production model → evidence correctness, tool-call correctness, policy/safety failures,
latency and token/cost profile measured → adversarial/prompt-injection tests → UAT deployment →
optional shadow/canary → acceptance → promote the model-routing configuration → monitor early
Production traffic → retain a rollback configuration. Embedding-model changes carry additional
controls — never mixing incompatible vector spaces in one index — building a new side-by-side vector
index, evaluating retrieval recall/precision against the old, running through UAT, and switching via an
atomic alias with the old index retained through the rollback window. **Current state.** Development model configuration changes have been exercised, but a full
Golden-Set-based before/after Production model-change comparison has not. The complete release
process remains **DESIGNED**; details are in Appendix B4.

**Target / remaining gap.** Govern actual provider versions and upgrade policies, monitor retirement
notices and validate replacements before retirement. Rollback requires the previous model to remain
available at the provider; retaining a routing configuration is not enough.

The additional response's AGG-01 decision-invariance check is useful: hold facts, approved knowledge
and executable rules fixed, then verify that changing an optional explanation model cannot alter the
decision. Record the actual Golden Set version, case count and results rather than treating the
source's "12 scenarios" as a permanent coverage guarantee. Overall acceptance rate alone is not enough:
release gates must separately cover safety, evidence fidelity and per-scenario regressions.

Also compare each proposed model use against a non-LLM baseline. For AGG-01 wording, compare with
fixed templates using answer correctness/clarity, engineer task time, correction rate, response time
and cost. For general chat, test grounding and permitted tool selection; for `Verify`, test false
rejections, missed defects and unavailable/malformed verdicts. A model verifier is an additional check,
not the source of ground truth for its own evaluation. Expert-approved expected outcomes remain needed.

## Q22 - Detecting obsolete rules/knowledge and preventing stale-knowledge recommendations

**Answer.** **BUILT:** approved-release resolution, immutable version pinning, integrity checks and
explicit lifecycle controls. OKF objects have identifiers, governance/approval metadata, version and
hash information. The PoTP authority validator checks approval, supersession, deprecated content,
hashes and projection readiness. These controls do not establish that every object has an effective
date, review deadline or equipment/software compatibility constraint, nor that those fields are
automatically enforced.

A new governed execution resolves the Active authority release and pins its exact version. Temporal
retries retain the original pin instead of silently switching to a newly Active release; the resolver
also supports lookup of that exact pinned version. Explicit revocation/supersession and validation
remain separate controls: a recorded pin is not blanket permission to use content rejected by policy.
Audit inspection of historical evidence is distinct from authorising a new recommendation.
Implementation evidence is in Appendix B2.

**DESIGNED:** mandatory effective-from/to and review-date metadata, version-compatibility enforcement,
automatic detection of obsolete source revisions, and a date-based staleness gate that refuses or
escalates expired knowledge. Activation of a new release does not by itself prove physical deletion
or tombstoning of old vector chunks/graph nodes. Projection retirement, historical retention and the
handling of in-flight workflows need explicit policy and tests. Until then, owners must review
currency and explicitly withdraw or supersede outdated knowledge.

## Q23 - Rollback of application, rules/knowledge, and model configuration

**Answer.** Rollback is designed as a first-class capability, independently for: application containers
(previous signed image/Helm release), agent definitions (versioned manifest), prompt templates
(versioned config), policy (versioned package, subject to compatibility), executable C# rules (the
compatible application image today; independent declarative rule bundles remain a target), OKF
knowledge bundles (immutable bundle plus alias), the vector index (alias switch), model
routing configuration (previous routing manifest), and model deployment (route to the previous
deployment where the provider version is still available); database schema migrations are rolled back
conditionally, following an expand/contract pattern with no destructive migration shipped in the same
release without a recovery plan. Independent rollback is only safe within a stated compatibility
matrix carried in the release manifest.

**Current state.** Development Kubernetes revision history exists (Appendix B4), but does not prove
a successful rollback across all dependent artifacts.

**Target / remaining gap.** Rehearse rollback against the deployed Production topology, proving image,
application configuration and compatible knowledge/model configuration recovery together.
A knowledge alias alone does
not roll back executable C# rule logic.

If a business-rule change spans OKF and C#, the release and rollback targets must retain a reviewed,
compatible pair of knowledge and application versions. Restoring only one side can recreate the
semantic drift described in Q6 even when all artifact hashes are valid.

## Q24 - Target response times and maximum interactive latency

**Answer.** The table contains **initial performance objectives to be validated in UAT**. These
candidate p95 design objectives are discussion inputs, not measured SLOs, contractual SLAs or
guaranteed maximum response times. They do not establish that the scenarios are operational.

| Use case / measurement | Candidate p95 objective | Scope / qualification |
|---|---:|---|
| AGG-05 chat, first token | 2s | Define whether authentication, retrieval and tool work are included |
| AGG-05 chat, complete streamed answer | 10s | Fix input/output length and tool-call workload |
| AGG-01 deterministic PoTP recommendation | 4s | Core decision, excluding optional model explanation |
| MW-02 link availability query | <10s | Validate against the actual integration |
| Mass generation, per-site processing | 5s | Individual site samples; also measure total batch duration |
| Consistency run | Not yet set | Asynchronous progress/status and completion objective to agree |
| Work Order DryRun preview, after approval | 2s | No persisted NCTSite write; excludes human review time |

**Current state.** No benchmark results establishing these values are asserted here. A DryRun result
cannot establish actual-write latency; any future authorised write path needs its own acceptance
objective and tests (Q16).

**Target / remaining gap.** UAT must measure p50/p95/p99 under agreed arrival rates, concurrency,
input/output lengths, provider RPM/TPM, queues and retries (Q3). Agree timeouts and asynchronous
transition thresholds separately; a timeout is not a guarantee of completion. Longer workflows
should use durable jobs with run IDs and progress. A `202 Accepted` response remains a target API
contract, not a verified behaviour of every scenario endpoint.

## Q25 - Monitoring and limiting LLM consumption/cost per user, use case and environment

**Answer.** Usage recording exists, but it is not a complete billing-attribution or budget-enforcement
system.

**Current state: BUILT.** The invocation ledger records usage and price-based estimates. End-user,
role and environment attribution is not guaranteed on every invocation; some paths identify the
calling service rather than the initiating engineer. Token usage is measured where the provider
reports it, with source/completeness recorded for missing or estimated values. Price-based cost
remains an estimate, not a reconciled Azure charge (Appendix B6).

**DESIGNED / incomplete:** complete per-user/use-case/environment cost reporting and budget enforcement.
Before a FinOps dashboard can provide reliable attribution, callers must consistently propagate
authenticated user or service identity, environment, use case, execution and trace correlation.
A chart over the current tables alone cannot fill missing identity data.

The target controls include separate environment/model quotas and budgets, per-user/role rate and
concurrency limits, per-use-case input/output and model-call budgets, permitted model classes,
and retrieval/context limits. Their enforcement and denial behaviour must be tested independently;
the ledger proves recording capability, not that all budget controls are active.

The source's assumption that AGG-05 consumes approximately 50% of tokens needs measurement. Provisioned
throughput is a capacity choice, not a complete spend cap; alerts are not request-denial controls.
Any hard budget must define whether new requests are refused, queued or degraded, cover fallback
routes, and be reconciled with actual billing. No such universal enforcement is asserted here.

## Q26 - Vulnerability patching and security updates for containers and platform components

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

## Q27 - Protecting curated views, MCP interfaces and deterministic rules from NCTSite/Oracle upgrades

**Answer.** The key is a stable anti-corruption/contract layer: NCTSite's internal tables feed curated
views/APIs, which feed the NCT-AI contract, which feeds MCP tools and deterministic rules — NCT-AI
never depends directly on arbitrary internal physical table layouts. Controls: versioned curated views,
an explicit interface contract, schema-compatibility tests, an Oracle contract test suite, test-data
fixtures, pre-upgrade UAT regression, a dependency manifest, backward-compatible view changes, and
change notification from the NCTSite/DB owners. Before an upgrade: object-existence tests, column-type
tests, sample-query tests, semantic data tests, the agent Golden Set, and performance tests all run; if
NCTSite must change a physical schema, the existing curated contract is maintained so NCT-AI continues
unchanged, and a new contract version is introduced only for an intentional breaking semantic change.
**DESIGNED**, matching this pattern; the curated views themselves exist and have been queried live
(Q16), which is stronger evidence than a design intention — what remains is an automated pre-upgrade
regression suite running the Oracle contract tests against a candidate schema before an upgrade ships;
today that is a manual re-run of the existing contract test suite. Passing the existing Golden Set
does not prove every schema change is transparent: unchanged field names/types can hide changed
units, null semantics, filtering or freshness. Extend the contract and regression cases for the actual
upgrade, and coordinate API/MCP/rule changes when the stable contract cannot be preserved.

## Q28 - Open-source/self-hosted vs managed/commercial components

**Answer.** The platform supports a mixed sourcing model. Open-source/self-hostable: .NET/ASP.NET,
Kubernetes, Istio, PostgreSQL, pgvector, Apache AGE (Apache 2.0), Temporal Server (MIT-licensed),
OpenTelemetry. Managed/commercial with an Azure equivalent: AKS (managed Kubernetes), Azure Database
for PostgreSQL (with pgvector and, on supported versions, Apache AGE), Azure Managed Redis, Azure
OpenAI/Foundry models, Entra ID, Azure Storage/ACR/Key Vault, and Azure Monitor/Dynatrace as an
OpenTelemetry backend option. Temporal Cloud exists as the managed alternative to self-hosting the MIT
server. Open source does not mean zero TCO — self-hosted components still need operations, backups,
monitoring, patching, capacity planning and engineering support. Final sourcing decisions belong to Telekom.

**Current target and history.** Neo4j was previously used/evaluated in development, including Aura
and live integration tests. The current target deployment standard is **Apache AGE only**; the Neo4j
adapter remains for interoperability/testing. This is a deployment decision, not a claim that Neo4j
was never deployed or a fresh inventory of running services. Appendix B7 separates the historical
evidence from the current standard.

**Target / remaining gap.** Confirm sourcing, supported versions and deployment compliance for each
customer environment before Production.
Provider abstraction reduces coupling; it does not eliminate migration effort or dependency on managed
identity, storage, networking and operational tooling. Existing subscriptions do not automatically
include every required entitlement or support contract.

For knowledge governance, **the capability is needed, not necessarily the specific OKF format**.
For a small, stable rule set, versioned documentation in Git plus a source/version/approval registry
could suffice. Replacing OKF would require adapting current contracts and preserving governance
checks, not simply deleting the knowledge files. OKF is more justified when many agents and scenarios
reuse the same curated knowledge through different retrieval methods. Choosing OKF does not itself
establish a need for a Knowledge Graph; graph-specific queries and dependencies must justify that
separate choice. These are design trade-offs, not a claim that the simpler replacement is implemented.

## Q29 - Licensing, support, recurring costs, and total TCO

**Answer.** A credible TCO figure needs the target Azure region, AKS SKU/node families, UAT and
Production availability requirements, PostgreSQL compute/storage, DR region, log retention, model
choice, token volume, pay-as-you-go vs provisioned throughput, Temporal self-hosted vs Cloud, support
contracts and any on-prem GPU option fixed first; a complete approved Production cost baseline is not
yet available. The ledger combines provider-reported usage where available with **estimated** cost
from a versioned price table, not freshly verified rates or a reconciled bill (Appendix B6).
Actual expenditure requires Azure billing/Cost Management data, contractual discounts,
capacity charges and reconciliation. Annual projections also need measured UAT volumes and documented
assumptions; neither a per-call estimate nor a historical retail rate establishes total TCO. Temporal's
software cost is genuinely zero if self-hosted (MIT-licensed server); the operational cost of running it
is not zero and is not yet estimated. The existing Azure AKS environment is intentionally stopped when
not in use, which materially reduces non-production compute cost while some supporting Azure services
continue to bill regardless — Production should not rely on stop/start as a cost-control mechanism,
because its HA/SLO posture differs. Cost buckets to model: AKS/compute, PostgreSQL, Redis, storage,
observability, Azure OpenAI/Foundry usage (input + output + embedding tokens, plus any provisioned
capacity), Temporal (software cost vs Cloud subscription), CI/CD and security tooling (license
entitlement and any incremental cost require confirmation), and DR (which materially increases TCO
through warm capacity, replicated data and redundant endpoints). The recommended next deliverable, not yet produced, is a
three-scenario cost model — Azure AI cost-optimised, Azure AI production HA/enterprise, and on-prem
GPU — priced from measured UAT tokens and requests rather than theoretical prompts.

**Commercial proposal from the additional response, not an approved quotation.** These numbers are
source-provided estimates, not newly researched market prices or a delivery commitment:

| Item | Proposed duration / scope | Indicative amount |
|---|---|---:|
| Phase 1 | Approximately 6 months; AGG-01 advisory-only pilot, no activated write path | EUR 180–220K development |
| Initial infrastructure setup | Provisioning effort; runtime billed separately | EUR 30–50K |
| Phase 2 | Approximately 9–12 months; AGG-05, AGG-03, MW-01, AGG-04, AGG-06 | EUR 350–450K |
| Phase 3 | Years 2–3; RAN-01, RAN-03, RAN-05, RAN-06 and AI-assisted licensing-table work | EUR 250–350K |
| Support after Year 1 | Year 1 proposed as included; coverage/hours and exclusions unconfirmed | EUR 80–120K/year |
| Runtime at Production peak | Azure infrastructure and model usage/capacity | EUR 15–30K/month |
| Source's stated three-year development/support envelope | Not an all-inclusive TCO | EUR 900K–1.1M |

**Arithmetic reconciliation is required.** Phases plus setup sum to EUR 810K–1.07M. If two subsequent
support years are additive, the subtotal becomes EUR 970K–1.31M, before runtime, tax and any excluded
items. Separately, 36 months at the stated peak runtime rate would be EUR 540K–1.08M; this is only a
constant-peak illustration, not a ramp-up forecast. The quoted EUR 900K–1.1M therefore cannot be used
as total three-year ownership cost. Confirm overlap/inclusions, staffing, acceptance criteria,
environments, rollout timing, discounts and support coverage before consolidating the budget. Neither
an existing Enterprise Agreement nor open-source licensing proves there are no additional licence costs.

The business case should separate the AGG-01 rules/governance baseline from optional LLM features.
Reading structured fields, applying fixed checks and showing a result is not by itself a reason to
require model inference. Budget and justify explanation, chat and verification calls separately using
the non-LLM comparisons in Q21; do not count deterministic automation gains as proven LLM gains.

## Q30 - New roles required to operate NCT-AI

**Answer.** NCT-AI needs an operating model, not only technical components. Proposed roles: **AI
Platform Owner** (roadmap, service ownership, production priorities, SLO/budget — likely Telekom
IT/platform organisation); **AI Product/Use Case Owner** (use-case value, business acceptance criteria,
priority, release acceptance — the relevant engineering/business domain); **Knowledge Author** (source
registration, candidate knowledge curation — an engineering domain team or delegated knowledge team);
**SME Reviewer** (verifying technical meaning and vendor interpretation — a Telekom engineering SME);
**Knowledge Approver** (approving knowledge/rule releases for Production, confirming source authority —
a designated senior Telekom knowledge/domain owner); **Rule/Agent Engineer** (deterministic rule
representation, agent definitions, tool contracts, test automation — the NCT-AI engineering team);
**AI/Model Ops** (model registry, routing, validation, cost monitoring, model release — the AI platform
team); **SRE/Platform Operations** (AKS, PostgreSQL, Redis, Temporal, monitoring, HA/DR, patching,
incident management — Telekom IT/cloud/platform operations); **Security/IAM Owner** (Entra ID, workload
identity, service authorisation, security policy, vulnerability process — Telekom Cyber Security/IAM);
**Integration/Data Contract Owner** (NCTSite curated views, interface versions, external contracts,
upgrade compatibility — the NCTSite/system integration team); **Audit/Compliance Reviewer** (audit
policy, retention review, evidence requirements, compliance verification — Telekom Security/Compliance).
Every governance gate this document describes — SME approval before a knowledge release activates, an
Approver's sign-off, a Security owner for the mesh-attestation work in Q14 — needs a named person
behind it before Production, not only a role on a slide. Naming the actual people or organisational
units against this list is a Telekom decision. The additional response also identifies a Knowledge
Reader access role, Site Constraint owners (Structural, Acquisition and EMF), and a Radio Planning
Team Lead for quality/backlog review. Add these to the responsibility matrix without assuming they
require new hires or can all be absorbed by existing staff. Workload, authority and separation of
author/reviewer duties must be assigned explicitly.
