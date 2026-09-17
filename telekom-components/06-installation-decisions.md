# NCT-AI / Installation Decisions

[Diagram index](index.md) | [Interactive HTML](06-installation-decisions.html) | [JSON specification](https://github.com/ist-com-gr/NCT-AI/blob/b83199798e25d1312f5fff95a36db386f72bb326/design/Prerequisites/diagram-lab/telekom-components/specs/06-installation-decisions.json)

**Source:** [Telekom components overview](https://github.com/ist-com-gr/NCT-AI/blob/b83199798e25d1312f5fff95a36db386f72bb326/design/Components/NCT-AI_Components_Overview_Telekom_2026-09-17.md), sections 4, 6. Snapshot: 2026-09-17.

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
- [Artifact storage registration](https://github.com/ist-com-gr/NCT-AI/blob/b83199798e25d1312f5fff95a36db386f72bb326/src/NCT.AI.Knowledge.Persistence/KnowledgeArtifactStorageRegistration.cs#L41)
- [Identity group verification](https://github.com/ist-com-gr/NCT-AI/blob/b83199798e25d1312f5fff95a36db386f72bb326/src/NCT.AI.AgUi.Web/Authentication/GraphGroupMembershipVerifier.cs#L60)

## Source Snapshot

The following excerpt is attributed to the source document, not newly verified live evidence. Any unresolved claims are qualified above.

<details>
<summary>Source sections 4, 6</summary>

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
