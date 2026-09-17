# NCT-AI / Experience Communication

[Diagram index](index.md) | [Interactive HTML](02-experience-communication.html) | [JSON specification](https://github.com/ist-com-gr/NCT-AI/blob/889128d85af7dcf478c9651e89df05d1288ec825/design/Prerequisites/diagram-lab/telekom-components/specs/02-experience-communication.json)

**Source:** [Telekom components overview](https://github.com/ist-com-gr/NCT-AI/blob/889128d85af7dcf478c9651e89df05d1288ec825/design/Components/NCT-AI_Components_Overview_Telekom_2026-09-17.md), sections 1, 3. Snapshot: 2026-09-17.

**View:** Selected calls. The BFF calls several backends directly; API is not a universal transit hop.

## Text Diagram

```text
[web] AG-UI Web
  --- HTTP(S) --> [bff] AG-UI / BFF

[bff] AG-UI / BFF
  --- HTTP / actions --> [api] API

[bff] AG-UI / BFF
  --- HTTP / reads --> [read-facades] Direct read facades

[bff] AG-UI / BFF
  --- HTTP / transcript --> [chat] Chat

[bff] AG-UI / BFF
  --- HTTP / ledger --> [models] Models
```

Arrows mean only the labeled relationship. They do not certify live traffic or a complete request trace.

## Components

| ID | Component | Context | Status / grouping |
|---|---|---|---|
| web | AG-UI Web | Engineer-facing console | Source-described |
| bff | AG-UI / BFF | Concern-specific HTTP clients | Source-described |
| api | API | Create / review / approve / cancel | Source-described |
| chat | Chat | Conversation read surface | Source-described |
| read-facades | Direct read facades | Runtime / Workflow / Knowledge | GROUPED |
| models | Models | Invocation ledger / capabilities | Source-described |

## Relationships

| From -> to | Meaning | Scope |
|---|---|---|
| web -> bff | HTTP(S) | Relationship as labeled |
| bff -> api | HTTP / actions | Relationship as labeled |
| bff -> read-facades | HTTP / reads | Relationship as labeled |
| bff -> chat | HTTP / transcript | Relationship as labeled |
| bff -> models | HTTP / ledger | Relationship as labeled |

## Evidence and Limits

- Direct read facades group Runtime, Workflow and Knowledge. Chat and Models are drawn separately to distinguish conversation reads from model-invocation accounting.
- Web-to-BFF is a logical application relationship. HTTP(S) does not assert TLS termination inside a specific process.
- The source communication table is not a complete browser transport map. Chat's streaming hub, callbacks and every API-to-runtime operation are outside this focused view, not prohibited by their omission.
- Mesh mTLS is an intended deployment property that must be verified from actual identities and policies. Pod readiness alone does not prove enforcement.

## Source Snapshot

The following excerpt is attributed to the source document, not newly verified live evidence. Any unresolved claims are qualified above.

<details>
<summary>Source sections 1, 3</summary>

## 1. Components — Full Feature Set

Telekom's pilot covers **every** component (no use case is excluded):


| Layer             | Component             | Role for Telekom                                                                                                                              |
| ----------------- | --------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| **Experience**    | AG-UI (BFF)           | Backend-for-frontend — calls Runtime, Workflow, Knowledge, Chat, Models and API directly, each for its own concern                            |
|                   | AG-UI (Web)           | Engineer-facing console — the surface Telekom staff use                                                                                       |
|                   | API                   | Agent/run management surface (create, review, approve, cancel) — one of several backends AG-UI calls, not a gateway everything passes through |
|                   | Chat                  | Conversational entry point, governed tool-calling — executes its own model calls in-process                                                   |
| **Agent Runtime** | Runtime               | Executes agents (including PoTP/MUX recommendations)                                                                                          |
|                   | Workflow              | Hosts/exposes the Temporal workflow definitions to the rest of the platform                                                                   |
|                   | Policy                | Governance rule library, consumed **in-process** by other hosts — not a network hop every action crosses                                      |
|                   | Context               | Assembles what an agent is allowed to see                                                                                                     |
| **Knowledge**     | Knowledge             | Registry of published knowledge bundles                                                                                                       |
|                   | Compiler              | Turns Telekom's raw sources into OKF bundles                                                                                                  |
|                   | Ingestion             | Pulls/normalizes Telekom's source material                                                                                                    |
|                   | Publisher             | Publishes approved bundles for use                                                                                                            |
| **Memory**        | Memory                | Cognitive memory capabilities (semantic/episodic) — chat conversation history is Chat's own store, not routed through here                    |
| **Models**        | Models                | Model routing/accounting — model provider is Telekom's choice                                                                                 |
| **Integration**   | Oracle MCP            | Governed, read-only access to NCTSite's Oracle data                                                                                           |
| **Orchestration** | Temporal              | Durable workflows — retries, human approvals                                                                                                  |
| **Data tier**     | PostgreSQL + pgvector | Relational + vector + graph store (single instance)                                                                                           |
|                   | Redis                 | Cache, session state                                                                                                                          |


⚠️ **15 Deployments does not mean 15 network hops on every business
action.** Some rows above are libraries consumed **in-process** by the
host that needs them (Policy is the clearest case); others are called
directly by AG-UI rather than by chaining through API. Treat this
table as component roles, not a request-flow diagram — the code is the
final source for which calls actually cross the network, and a
dedicated flow map (if needed) should be built from it as a separate,
verified artifact.

## 3. How Components Communicate

Verified against the code (`gen-manifests.sh`'s service list for PostgreSQL wiring, `Program.cs` for
Redis/Temporal/HTTP clients — not inferred from the architecture diagram):


| Component                      | Talks to                                        | Protocol / technology                                                                                |
| ------------------------------ | ----------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| AG-UI (BFF)                    | Runtime, Workflow, Knowledge, Chat, Models, API | HTTP/JSON over the Istio mesh (mTLS between sidecars)                                                |
| AG-UI (BFF)                    | PostgreSQL (`nct_ai`)                           | Npgsql/EF Core, TCP 5432, TLS                                                                        |
| AG-UI (Web)                    | AG-UI (BFF)                                     | HTTPS, browser-facing                                                                                |
| AG-UI (Web)                    | Redis                                           | StackExchange.Redis, TCP — corrected: this row was missed in the first pass                          |
| API                            | PostgreSQL (`nct_ai`, vector)                   | Npgsql/EF Core, TCP 5432, TLS                                                                        |
| API                            | Redis                                           | StackExchange.Redis (RESP), TCP                                                                      |
| Chat                           | PostgreSQL (`nct_ai`)                           | Npgsql/EF Core, TCP 5432, TLS — conversation store                                                   |
| Chat                           | Redis                                           | StackExchange.Redis, TCP                                                                             |
| Chat                           | Model provider                                  | HTTPS, called **in-process** — no hop to the Models pod                                              |
| Runtime                        | PostgreSQL (`nct_ai`)                           | Npgsql/EF Core, TCP 5432, TLS                                                                        |
| Runtime                        | Temporal                                        | gRPC (Temporal .NET SDK), TLS                                                                        |
| Runtime                        | Oracle MCP                                      | HTTP (MCP protocol), over the Istio mesh                                                             |
| Workflow                       | PostgreSQL (`nct_ai`)                           | Npgsql/EF Core, TCP 5432, TLS                                                                        |
| Workflow                       | Temporal                                        | gRPC (Temporal .NET SDK worker), TLS                                                                 |
| Workflow                       | Ingestion, Compiler, Publisher                  | HTTP, over the Istio mesh — the EKC (Knowledge Compiler) pipeline stage client                       |
| Context                        | PostgreSQL (`nct_ai`, vector)                   | Npgsql/EF Core, TCP 5432, TLS                                                                        |
| Context                        | Redis                                           | StackExchange.Redis, TCP                                                                             |
| Knowledge                      | PostgreSQL (`nct_ai`, vector)                   | Npgsql/EF Core, TCP 5432, TLS                                                                        |
| Knowledge                      | Publisher                                       | HTTP, over the Istio mesh                                                                            |
| Memory                         | PostgreSQL (`nct_ai`)                           | Npgsql/EF Core, TCP 5432, TLS                                                                        |
| Oracle MCP                     | PostgreSQL (`nct_ai`)                           | Npgsql/EF Core, TCP 5432, TLS — dataset audit                                                        |
| Oracle MCP                     | NCTSITE Oracle ADB                              | Oracle Net (ODP.NET), TCP 1522, mTLS via wallet                                                      |
| Models                         | Model provider                                  | HTTPS                                                                                                |
| Compiler, Ingestion, Publisher | *(none — inbound only)*                         | Expose HTTP endpoints (`/compile`, `/ingest`, `/publish`, etc.); make no outbound calls of their own |
|                                |                                                 |                                                                                                      |


⚠️ **Fully verified this pass**, including the three components
flagged unconfirmed before: Policy makes no network calls (in-process
governance library, 0 endpoints); Compiler/Ingestion/Publisher are
called by Workflow's EKC stage client and (Publisher only) by
Knowledge, and make no outbound calls themselves; Temporal is reached
by exactly two hosts, Runtime and Workflow (`TemporalClient`/worker),
confirmed by grep, no others.

⚠️ **Every pod-to-pod HTTP call above rides the Istio mesh**, so it is
mTLS-encrypted regardless of the row not repeating it. PostgreSQL,
Redis and Oracle connections are **not** mesh-internal — they cross to
managed services outside the cluster and carry their own TLS.

</details>
