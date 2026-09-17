# Complete Component Communications

[Diagram index](index.md) | [Interactive HTML](09-complete-communications.html) | [JSON specification](https://github.com/ist-com-gr/NCT-AI/blob/889128d85af7dcf478c9651e89df05d1288ec825/design/Prerequisites/diagram-lab/telekom-components/specs/09-complete-communications.json)

**Source:** [Telekom components overview](https://github.com/ist-com-gr/NCT-AI/blob/889128d85af7dcf478c9651e89df05d1288ec825/design/Components/NCT-AI_Components_Overview_Telekom_2026-09-17.md), sections 3. Snapshot: 2026-09-17.

**View:** Complete communication map. Every connection in section 3: 30 directed links across 19 participants, grouped into protocol lanes with every caller and target named.

## Text Diagram

```text
AG-UI Web -- HTTPS --> AG-UI BFF
AG-UI BFF -- HTTP/JSON / mesh --> Runtime
AG-UI BFF -- HTTP/JSON / mesh --> Workflow
AG-UI BFF -- HTTP/JSON / mesh --> Knowledge
AG-UI BFF -- HTTP/JSON / mesh --> Chat
AG-UI BFF -- HTTP/JSON / mesh --> Models
AG-UI BFF -- HTTP/JSON / mesh --> API
AG-UI BFF -- SQL / TLS :5432 --> PostgreSQL
API -- SQL / TLS :5432 --> PostgreSQL
Chat -- SQL / TLS :5432 --> PostgreSQL
Runtime -- SQL / TLS :5432 --> PostgreSQL
Workflow -- SQL / TLS :5432 --> PostgreSQL
Context -- SQL / TLS :5432 --> PostgreSQL
Knowledge -- SQL / TLS :5432 --> PostgreSQL
Memory -- SQL / TLS :5432 --> PostgreSQL
Oracle MCP -- SQL / TLS :5432 --> PostgreSQL
AG-UI Web -- RESP / TCP --> Redis
API -- RESP / TCP --> Redis
Chat -- RESP / TCP --> Redis
Context -- RESP / TCP --> Redis
Runtime -- gRPC / TLS --> Temporal
Workflow -- gRPC / TLS --> Temporal
Runtime -- HTTP / MCP --> Oracle MCP
Oracle MCP -- 1522 / mTLS --> NCTSITE Oracle ADB
Workflow -- HTTP / mesh --> Ingestion
Workflow -- HTTP / mesh --> Compiler
Workflow -- HTTP / mesh --> Publisher
Knowledge -- HTTP / mesh --> Publisher
Chat -- HTTPS --> Model provider
Models -- HTTPS --> Model provider
```

Arrows mean only the labeled relationship. They do not certify live traffic or a complete request trace.

## Components

| ID | Component | Context | Status / grouping |
|---|---|---|---|
| web | AG-UI Web | Browser-facing experience | Source-described |
| browser_bff | AG-UI BFF | Experience backend | Source-described |
| bff | AG-UI BFF | Direct backend calls | Source-described |
| bff_targets | Runtime / Workflow / Knowledge | Chat / Models / API | ALL 6 TARGETS |
| pg_clients | BFF / API / Chat / Runtime / Workflow | Context / Knowledge / Memory / Oracle MCP | ALL 9 CLIENTS |
| postgres | PostgreSQL / nct_ai | Npgsql / EF Core / vector | Source-described |
| redis_clients | AG-UI Web / API / Chat / Context | StackExchange.Redis | ALL 4 CLIENTS |
| redis | Redis | Cache / sessions | Source-described |
| temporal_clients | Runtime / Workflow | Temporal .NET SDK / Workflow worker | BOTH CLIENTS |
| temporal | Temporal | Durable orchestration | Source-described |
| runtime | Runtime | Operational reads | Source-described |
| oracle_mcp | Oracle MCP | Read-only connector | Source-described |
| oracle | NCTSITE Oracle ADB | ODP.NET / Oracle Net | Source-described |
| workflow | Workflow | EKC stage HTTP client | Source-described |
| stage_targets | Ingestion / Compiler | Publisher | 3 INBOUND-ONLY HOSTS |
| knowledge | Knowledge | Published bundle registry | Source-described |
| publisher | Publisher | HTTP /publish | INBOUND ONLY |
| model_clients | Chat / Models | Chat calls in-process; no Models pod hop | 2 DIRECT PROVIDER CLIENTS |
| provider | Model provider | Provider HTTPS API | Source-described |

## Relationships

| From -> to | Meaning | Scope |
|---|---|---|
| web -> browser_bff | HTTPS | Relationship as labeled |
| bff -> bff_targets | HTTP/JSON / mesh | Relationship as labeled |
| pg_clients -> postgres | SQL / TLS :5432 | Relationship as labeled |
| redis_clients -> redis | RESP / TCP | Relationship as labeled |
| temporal_clients -> temporal | gRPC / TLS | Relationship as labeled |
| runtime -> oracle_mcp | HTTP / MCP | Relationship as labeled |
| oracle_mcp -> oracle | 1522 / mTLS | Relationship as labeled |
| workflow -> stage_targets | HTTP / mesh | Relationship as labeled |
| knowledge -> publisher | HTTP / mesh | Relationship as labeled |
| model_clients -> provider | HTTPS | Relationship as labeled |

## Evidence and Limits

- Each lane represents every named caller connecting to every named target in that lane. Repeated names refer to the same component, not additional deployments. The companion expands all 30 links individually.
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
