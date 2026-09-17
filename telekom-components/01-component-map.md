# NCT-AI / Telekom Component Map

[Diagram index](index.md) | [Interactive HTML](01-component-map.html) | [JSON specification](https://github.com/ist-com-gr/NCT-AI/blob/31b7ec777623d9e3e4b179f82d5df9a16891fcc5/design/Prerequisites/diagram-lab/telekom-components/specs/01-component-map.json)

**Source:** [Telekom components overview](https://github.com/ist-com-gr/NCT-AI/blob/31b7ec777623d9e3e4b179f82d5df9a16891fcc5/design/Components/NCT-AI_Components_Overview_Telekom_2026-09-17.md), sections 1. Snapshot: 2026-09-17.

**View:** Inventory. All 15 application components, grouped by role rather than presented as a mandatory request chain.

## Text Diagram

```text
[experience] AG-UI Web + BFF
  Console / frontend backend [2 COMPONENTS]

[api] API
  Agent and run management [1 COMPONENT]

[chat] Chat
  Conversation / governed tools [1 COMPONENT]

[runtime] Runtime
  Agent execution / PoTP MUX [1 COMPONENT]

[workflow] Workflow
  Temporal workflows / workers [1 COMPONENT]

[policy] Policy
  In-process governance [1 COMPONENT]

[context] Context
  Authorized context assembly [1 COMPONENT]

[memory] Memory
  Semantic / episodic capabilities [1 COMPONENT]

[knowledge] Knowledge
  Published knowledge registry [1 COMPONENT]

[knowledge-stages] Knowledge stages
  Ingestion / Compiler / Publisher [3 COMPONENTS]

[models] Models
  Routing / invocation accounting [1 COMPONENT]

[oracle-mcp] Oracle MCP
  Governed read-only NCTSite access [1 COMPONENT]
```

No arrows are drawn: this is an inventory, not a runtime call graph.

## Components

| ID | Component | Context | Status / grouping |
|---|---|---|---|
| experience | AG-UI Web + BFF | Console / frontend backend | 2 COMPONENTS |
| api | API | Agent and run management | 1 COMPONENT |
| chat | Chat | Conversation / governed tools | 1 COMPONENT |
| runtime | Runtime | Agent execution / PoTP MUX | 1 COMPONENT |
| workflow | Workflow | Temporal workflows / workers | 1 COMPONENT |
| policy | Policy | In-process governance | 1 COMPONENT |
| context | Context | Authorized context assembly | 1 COMPONENT |
| memory | Memory | Semantic / episodic capabilities | 1 COMPONENT |
| knowledge | Knowledge | Published knowledge registry | 1 COMPONENT |
| knowledge-stages | Knowledge stages | Ingestion / Compiler / Publisher | 3 COMPONENTS |
| models | Models | Routing / invocation accounting | 1 COMPONENT |
| oracle-mcp | Oracle MCP | Governed read-only NCTSite access | 1 COMPONENT |

## Evidence and Limits

- AG-UI Web and AG-UI/BFF are two separate components. Ingestion, Compiler and Publisher are three separate stage hosts. The 12 drawn boxes therefore represent 15 components.
- Temporal, PostgreSQL, Redis, identity, model providers and telemetry are supporting dependencies, not part of the 15-component count.
- Policy is consumed in-process. Deploying a Policy host does not prove that business actions traverse it over the network.
- The source describes full pilot scope, not evidence that every specified use case is operational. Chat conversation history is not interchangeable with Memory's semantic/episodic capabilities.

## Source Snapshot

The following excerpt is attributed to the source document, not newly verified live evidence. Any unresolved claims are qualified above.

<details>
<summary>Source sections 1</summary>

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

</details>
