# NCT-AI / Integration Paths

[Diagram index](index.md) | [Interactive HTML](03-integration-paths.html) | [JSON specification](https://github.com/ist-com-gr/NCT-AI/blob/31b7ec777623d9e3e4b179f82d5df9a16891fcc5/design/Prerequisites/diagram-lab/telekom-components/specs/03-integration-paths.json)

**Source:** [Telekom components overview](https://github.com/ist-com-gr/NCT-AI/blob/31b7ec777623d9e3e4b179f82d5df9a16891fcc5/design/Components/NCT-AI_Components_Overview_Telekom_2026-09-17.md), sections 3. Snapshot: 2026-09-17.

**View:** Selected calls. Operational reads, Temporal workers, knowledge-stage calls and in-process model execution are separate paths.

## Text Diagram

```text
[runtime] Runtime
  --- HTTP / MCP --> [oracle-mcp] Oracle MCP

[oracle-mcp] Oracle MCP
  --- 1522 / mTLS --> [oracle] NCTSite Oracle ADB

[runtime] Runtime
  --- gRPC / TLS --> [temporal] Temporal

[workflow] Workflow
  --- gRPC / TLS --> [temporal] Temporal

[workflow] Workflow
  --- HTTP / EKC --> [ekc-stages] Knowledge stages

[chat] Chat
  --- in-process --> [model-core] Models.Core

[model-core] Models.Core
  --- HTTPS --> [provider] Model provider
```

Arrows mean only the labeled relationship. They do not certify live traffic or a complete request trace.

## Components

| ID | Component | Context | Status / grouping |
|---|---|---|---|
| runtime | Runtime | PoTP / MUX agent execution | Source-described |
| oracle-mcp | Oracle MCP | SELECT-only / allowlisted access | Source-described |
| oracle | NCTSite Oracle ADB | External operational data | Source-described |
| temporal | Temporal | Durable execution and retries | Source-described |
| workflow | Workflow | Knowledge workflow host | Source-described |
| ekc-stages | Knowledge stages | Ingestion / Compiler / Publisher | 3 HOSTS |
| chat | Chat | Conversation execution host | Source-described |
| model-core | Models.Core | Router / policy / provider adapter | INSIDE CHAT |
| provider | Model provider | Azure OpenAI implemented today | Source-described |

## Relationships

| From -> to | Meaning | Scope |
|---|---|---|
| runtime -> oracle-mcp | HTTP / MCP | Relationship as labeled |
| oracle-mcp -> oracle | 1522 / mTLS | Relationship as labeled |
| runtime -> temporal | gRPC / TLS | Relationship as labeled |
| workflow -> temporal | gRPC / TLS | Relationship as labeled |
| workflow -> ekc-stages | HTTP / EKC | Relationship as labeled |
| chat -> model-core | in-process | In-process governed call |
| model-core -> provider | HTTPS | Relationship as labeled |

## Evidence and Limits

- Models.Core denotes a library within the Chat process, not the separately deployed Models host. Policy enforcement also need not be a network hop.
- Workflow calls the Ingestion, Compiler and Publisher hosts. Grouping these endpoints does not imply they call each other directly. Knowledge also calls Publisher, as recorded in the source table.
- The source lists PostgreSQL clients in BFF, API, Chat, Runtime, Workflow, Context, Knowledge, Memory and Oracle MCP. It lists Redis clients in AG-UI Web, API, Chat and Context. These dependencies are retained in the source excerpt rather than added as crossing arrows.
- The Models host also has a provider connection according to the source. This focused view does not assert that it executes Chat's requests remotely.
- The source says only Runtime and Workflow connect to Temporal. This is a statement attributed to the source, not a new exhaustive code or live-traffic verification.
- Future real Work Order writes are not part of the read-only Oracle path. No live NCTSite write capability is certified here.

## Source Snapshot

The following excerpt is attributed to the source document, not newly verified live evidence. Any unresolved claims are qualified above.

<details>
<summary>Source sections 3</summary>

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
