# Complete Component Communications

[Diagram index](index.md) | [Interactive HTML](09-complete-communications.html) | [JSON specification](https://github.com/ist-com-gr/NCT-AI/blob/31b7ec777623d9e3e4b179f82d5df9a16891fcc5/design/Prerequisites/diagram-lab/telekom-components/specs/09-complete-communications.json)

**Source:** [Telekom components overview](https://github.com/ist-com-gr/NCT-AI/blob/31b7ec777623d9e3e4b179f82d5df9a16891fcc5/design/Components/NCT-AI_Components_Overview_Telekom_2026-09-17.md), sections 3. Snapshot: 2026-09-17.

**View:** Complete communication map. Every connection in section 3: 19 unique components with 30 individually drawn directed links.

## Text Diagram

```text
AG-UI BFF -- HTTP --> Runtime
AG-UI BFF -- HTTP --> Workflow
AG-UI BFF -- HTTP --> Knowledge
AG-UI BFF -- HTTP --> Chat
AG-UI BFF -- HTTP --> Models
AG-UI BFF -- HTTP --> API
AG-UI BFF -- SQL --> PostgreSQL
AG-UI Web -- HTTPS --> AG-UI BFF
AG-UI Web -- RESP --> Redis
API -- SQL --> PostgreSQL
API -- RESP --> Redis
Chat -- SQL --> PostgreSQL
Chat -- RESP --> Redis
Chat -- HTTPS --> Model provider
Runtime -- SQL --> PostgreSQL
Runtime -- gRPC --> Temporal
Runtime -- MCP --> Oracle MCP
Workflow -- SQL --> PostgreSQL
Workflow -- gRPC --> Temporal
Workflow -- HTTP --> Ingestion
Workflow -- HTTP --> Compiler
Workflow -- HTTP --> Publisher
Context -- SQL --> PostgreSQL
Context -- RESP --> Redis
Knowledge -- SQL --> PostgreSQL
Knowledge -- HTTP --> Publisher
Memory -- SQL --> PostgreSQL
Oracle MCP -- SQL --> PostgreSQL
Oracle MCP -- Oracle Net --> NCTSITE Oracle ADB
Models -- HTTPS --> Model provider
```

Arrows mean only the labeled relationship. They do not certify live traffic or a complete request trace.

## Components

| ID | Component | Context | Status / grouping |
|---|---|---|---|
| web | AG-UI Web |  | Source-described |
| bff | AG-UI BFF |  | Source-described |
| api | API |  | Source-described |
| chat | Chat |  | Source-described |
| models | Models |  | Source-described |
| runtime | Runtime |  | Source-described |
| workflow | Workflow |  | Source-described |
| knowledge | Knowledge |  | Source-described |
| context | Context |  | Source-described |
| memory | Memory |  | Source-described |
| postgres | PostgreSQL |  | Source-described |
| redis | Redis |  | Source-described |
| temporal | Temporal |  | Source-described |
| oracle_mcp | Oracle MCP |  | Source-described |
| oracle | NCTSITE Oracle ADB |  | Source-described |
| provider | Model provider |  | Source-described |
| ingestion | Ingestion |  | Source-described |
| compiler | Compiler |  | Source-described |
| publisher | Publisher |  | Source-described |

## Relationships

| From -> to | Meaning | Scope |
|---|---|---|
| bff -> runtime | HTTP | Relationship as labeled |
| bff -> workflow | HTTP | Relationship as labeled |
| bff -> knowledge | HTTP | Relationship as labeled |
| bff -> chat | HTTP | Relationship as labeled |
| bff -> models | HTTP | Relationship as labeled |
| bff -> api | HTTP | Relationship as labeled |
| bff -> postgres | SQL | Relationship as labeled |
| web -> bff | HTTPS | Relationship as labeled |
| web -> redis | RESP | Relationship as labeled |
| api -> postgres | SQL | Relationship as labeled |
| api -> redis | RESP | Relationship as labeled |
| chat -> postgres | SQL | Relationship as labeled |
| chat -> redis | RESP | Relationship as labeled |
| chat -> provider | HTTPS | Relationship as labeled |
| runtime -> postgres | SQL | Relationship as labeled |
| runtime -> temporal | gRPC | Relationship as labeled |
| runtime -> oracle_mcp | MCP | Relationship as labeled |
| workflow -> postgres | SQL | Relationship as labeled |
| workflow -> temporal | gRPC | Relationship as labeled |
| workflow -> ingestion | HTTP | Relationship as labeled |
| workflow -> compiler | HTTP | Relationship as labeled |
| workflow -> publisher | HTTP | Relationship as labeled |
| context -> postgres | SQL | Relationship as labeled |
| context -> redis | RESP | Relationship as labeled |
| knowledge -> postgres | SQL | Relationship as labeled |
| knowledge -> publisher | HTTP | Relationship as labeled |
| memory -> postgres | SQL | Relationship as labeled |
| oracle_mcp -> postgres | SQL | Relationship as labeled |
| oracle_mcp -> oracle | Oracle Net | Relationship as labeled |
| models -> provider | HTTPS | Relationship as labeled |

## Evidence and Limits

- Each component appears exactly once. All 30 source-table connections are drawn individually; a line crossing without a node is not a junction. This is the fourth view; its original filename is retained for existing links.
- PostgreSQL has nine listed clients. API, Context and Knowledge use vector data; Chat uses conversation storage; Oracle MCP records dataset audit. Redis has four clients, including AG-UI Web.
- Runtime and Workflow both connect to Temporal. Workflow calls Ingestion, Compiler and Publisher; Knowledge also calls Publisher. Those three stage hosts are inbound-only in the supplied table.
- Chat and Models make separate direct HTTPS calls to the model provider. Chat's model execution is in-process: there is no Chat-to-Models pod hop. Policy is not an added network hop.
- Service HTTP uses the Istio mesh with source-described sidecar mTLS. Oracle uses ODP.NET / Oracle Net on TCP 1522 with wallet mTLS. These are source assertions, not newly measured live connections.
- Complete means complete against section 3 of this source snapshot, not against every possible application integration. The existing selected-path diagrams remain available.

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
