# NCT-AI / State and Availability

[Diagram index](index.md) | [Interactive HTML](05-state-availability.html) | [JSON specification](https://github.com/ist-com-gr/NCT-AI/blob/889128d85af7dcf478c9651e89df05d1288ec825/design/Prerequisites/diagram-lab/telekom-components/specs/05-state-availability.json)

**Source:** [Telekom components overview](https://github.com/ist-com-gr/NCT-AI/blob/889128d85af7dcf478c9651e89df05d1288ec825/design/Components/NCT-AI_Components_Overview_Telekom_2026-09-17.md), sections 2. Snapshot: 2026-09-17.

**View:** Pilot target. Replica placement, durable data, Redis keys and active sessions have distinct failure and recovery requirements.

## Text Diagram

```text
[service] Critical service
  - - place replica 1 --> [node-a] Eligible node A

[service] Critical service
  - - place replica 2 --> [node-b] Eligible node B
```

Arrows mean only the labeled relationship. They do not certify live traffic or a complete request trace.

## Components

| ID | Component | Context | Status / grouping |
|---|---|---|---|
| node-a | Eligible node A | Required label / taint tolerance | PILOT TARGET |
| service | Critical service | Two replicas / explicit spread | PILOT TARGET |
| node-b | Eligible node B | Separate failure domain | PILOT TARGET |
| postgres | PostgreSQL | nct_ai + nct_temporal databases | SHARED INSTANCE |
| redis | Redis | Cache / key ring / idempotency | MIXED CRITICALITY |
| session | Active user session | Affinity / reconnection behavior | TEST REQUIRED |

## Relationships

| From -> to | Meaning | Scope |
|---|---|---|
| service -> node-a | place replica 1 | Conditional / proposed |
| service -> node-b | place replica 2 | Conditional / proposed |

## Evidence and Limits

- The arrows describe proposed scheduling placement, not HTTP traffic. Two replicas require two eligible nodes plus explicit spread constraints; three cluster nodes alone do not establish this.
- Source DEV reference: three system nodes, one runtime node and two memory nodes. A runtime node selector cannot use the other pools merely because they exist.
- PostgreSQL baseline: 8 vCPU, 32 GB RAM, 500 GB SSD. Larger baseline: 16 vCPU, 64 GB, 1 TB or more. The nct_ai/nct_temporal split is a planning allocation, not enforced per-database CPU/RAM isolation.
- Redis baseline: 2-4 vCPU and 8-16 GB RAM; higher load: 4-8 vCPU and 16-32 GB. Do not treat every key as a disposable cache entry.
- Loss of the shared Data Protection key ring invalidates protected sessions. Lost idempotency records remove API duplicate suppression within its window, without proving that downstream systems will accept a duplicate write.
- Application spread does not provide PostgreSQL or Redis failover, preserve an active connection, or establish a recovery-time objective. Backup/restore, session reconnection and single-node loss require independent tests.

## Source Snapshot

The following excerpt is attributed to the source document, not newly verified live evidence. Any unresolved claims are qualified above.

<details>
<summary>Source sections 2</summary>

## 2. Initial Application Resource Budget (No Operational Add-Ons)

⛔ **This is an application-container budget, not a cluster sizing and
not the pilot's total footprint.** It counts only the containers
listed below, at their **requests/limits as declared in the
manifests** — it excludes Istio sidecars, ingress, mesh control plane
(istiod), telemetry/observability, Kubernetes system overhead, upgrade
and failure headroom, and PostgreSQL/Redis if either is ever run
inside the cluster rather than as a managed service. Provision from
this table only after adding those categories.

Each of the 15 NCT-AI components above runs as its own Kubernetes
Deployment, minimum **2 replicas** (figures below are per-replica;
multiply by 2 for the Deployment total):


| Service                       | Replicas | CPU request (ea.) | Memory request (ea.) | CPU limit (ea.) | Memory limit (ea.) |
| ----------------------------- | -------- | ----------------- | -------------------- | --------------- | ------------------ |
| AG-UI                         | 2        | 100m              | 256Mi                | 1               | 1Gi                |
| AG-UI (Web)                   | 2        | 100m              | 256Mi                | 1               | 1Gi                |
| API / BFF                     | 2        | 100m              | 256Mi                | 1               | 1Gi                |
| Chat                          | 2        | 100m              | 512Mi                | 1               | 1.5Gi              |
| Runtime                       | 2        | 100m              | 256Mi                | 1               | 1Gi                |
| Policy                        | 2        | 100m              | 256Mi                | 1               | 1Gi                |
| Context                       | 2        | 100m              | 256Mi                | 1               | 1Gi                |
| Knowledge                     | 2        | 100m              | 256Mi                | 1               | 1Gi                |
| Compiler                      | 2        | 100m              | 256Mi                | 1               | 1Gi                |
| Ingestion                     | 2        | 100m              | 256Mi                | 1               | 1Gi                |
| Publisher                     | 2        | 100m              | 256Mi                | 1               | 1Gi                |
| Memory                        | 2        | 100m              | 256Mi                | 1               | 1Gi                |
| Models                        | 2        | 100m              | 256Mi                | 1               | 1Gi                |
| Oracle MCP                    | 2        | 100m              | 256Mi                | 1               | 1Gi                |
| Workflow                      | 2        | 100m              | 256Mi                | 1               | 1Gi                |
| **Subtotal (30 NCT-AI pods)** | **30**   | **\~3 CPU**       | **8 GiB**            | **\~30 CPU**    | **31 GiB**         |


⛔ **Temporal is 6 roles — checked against the live cluster directly,
not the Helm values file.** `temporal/temporal` is
installed with no chart version pinned (`helm upgrade --install temporal temporal/temporal`, no `--version`), and the official chart
creates one Deployment **per active server role**, not one combined
process. `kubectl get deploy -n temporal` on the dev cluster confirms
exactly that — 6 Deployments, each 1 replica today:

`kubectl get deploy -n temporal -o json` read back **`None` for every
container's `requests` and `limits`** in the dev deployment — the
chart's own `values.yaml` ships `resources: {}` for every role, with a
comment stating the maintainers deliberately do not set a default,
leaving it "a conscious choice for the user." There is no official
per-role recommendation from Temporal for a self-hosted cluster; the
only concrete community data point found is a real incident where
History crashed with `OOMKiller` at 0.25 CPU / 0.5Gi, and a separate
large-scale reference (3× Frontend at 4 cores/4Gi, 5× History at 8
cores/8Gi, 3× Matching at 4 cores/4Gi, 2× Worker at 4 cores/4Gi) that
its own poster called "a bit high" for a low-throughput deployment.

**Pilot-scale proposal below** — sized between "the chart's own
Minikube-scale example" and "the community's high-throughput
reference," and *deliberately higher than the OOM-crashed 0.25
CPU/0.5Gi on History* since that failure is the one hard data point
this search produced. Treat as a starting point to load-test, not a
Temporal-endorsed number:


| Temporal role          | Replicas (pilot floor) | CPU/Memory request (ea.) | CPU/Memory limit (ea.)  |
| ---------------------- | ---------------------- | ------------------------ | ----------------------- |
| Frontend               | 2                      | 500m / 1Gi               | 1 / 2Gi                 |
| History                | 2                      | 1 / 2Gi                  | 2 / 4Gi                 |
| Matching               | 2                      | 500m / 1Gi               | 1 / 2Gi                 |
| Worker (server-side)   | 2                      | 500m / 1Gi               | 1 / 2Gi                 |
| Web UI                 | 2                      | 250m / 512Mi             | 500m / 1Gi              |
| Admin tools            | 2                      | 100m / 256Mi             | 250m / 512Mi            |
| **Subtotal (12 pods)** | **12**                 | **\~5.7 CPU / 11.5 GiB** | **\~11.5 CPU / 23 GiB** |


**Total application budget, this table plus § above: 42 pods, \~8.7
CPU / 19.5 GiB requested, \~41.5 CPU / 54 GiB at limit** — still
excluding every operational add-on named at the top of this section.

Every application pod (NCT-AI and Temporal alike) also carries an
Istio sidecar for mesh traffic and mTLS, **not counted anywhere in
this table** — "2/2 Running" identifies a container count, not its
CPU/memory, and is not evidence either way about sidecar cost.

**PostgreSQL — one instance, two databases:**


|                                                       | Baseline                | Larger baseline          |
| ----------------------------------------------------- | ----------------------- | ------------------------ |
| Instance vCPU                                         | 8                       | 16                       |
| Instance RAM                                          | 32 GB                   | 64 GB                    |
| Instance storage                                      | 500 GB SSD              | 1 TB+ SSD                |
| `nct_ai` (relational + `pgvector` + Apache AGE graph) | 6 vCPU / 24 GB / 400 GB | 12 vCPU / 48 GB / 800 GB |
| `nct_temporal` (Temporal persistence)                 | 2 vCPU / 8 GB / 100 GB  | 4 vCPU / 16 GB / 200 GB  |


Primary/standby HA where required, with backup capacity kept separate
from primary data capacity. `nct_ai` carries the bulk of the load
(relational data, `pgvector` and the Apache AGE graph store together —
no separate graph database to size); `nct_temporal` is comparatively
light, sized from workflow/event volume rather than from NCT-AI's
data. The split above is a starting allocation on the shared instance,
not a hard partition — re-derive from measured load per database.

**Redis:**


|      | Baseline | Higher load |
| ---- | -------- | ----------- |
| vCPU | 2–4      | 4–8         |
| RAM  | 8–16 GB  | 16–32 GB    |


⛔ **Correction: not all-ephemeral.** Cache and rate-limit counters are
reconstructible without cost. Two keys are not:

- **AG-UI (Web)'s Data Protection key ring** (`nct-ai:agui:dataprotection-keys`)
— also protects cookies and the encrypted MSAL token cache. Losing it
invalidates every live session and forces re-authentication.
- **API's idempotency records** (`RedisApiIdempotencyStore`) — if a key
is evicted or lost, a retried request can be treated as new. This
does not by itself prove a duplicate write reaches NCTSite; a
downstream service may still catch it, but the API's own
duplicate-suppression window is gone.

Separate reconstructible cache from operationally-critical keys, and
agree retention/expiry/eviction/recovery per category, with durable
key storage where warranted. Use HA where required, with an explicit
persistence and eviction policy per category — not one blanket policy
for all of Redis.

**Temporal runs on the same Kubernetes cluster** as the NCT-AI services
above, not as a separate managed service — as 6 role Deployments
(Frontend, History, Matching, Worker, Web UI, Admin tools; see § above
for the measured live topology), minimum **2 replicas each**,
consistent with the floor set for the rest of the platform.

⚠️ **A 3-node cluster total does not by itself tolerate a node loss —
node count and node *eligibility* are different requirements.** Two
pods land on two nodes, not spread across three. This is a real
constraint of the **current dev environment specifically**: all 15
NCT-AI Deployments there carry `nodeSelector: { workload: runtime }` —
pinned to one specific node pool — and that pool has exactly 1 node in
the dev reference cluster today (the system and memory pools are not
eligible for these pods regardless of their own size). That is a dev
configuration choice, not a property of NCT-AI's design; a pilot
cluster built for Telekom would provision the pool a critical
service's replicas run on with 2+ nodes from the start. Stated here so
node count and eligible-pool sizing are planned together rather than
assumed from one number.

**What is actually needed:** at least 2 nodes **eligible for each
critical service** (here, the `runtime`-labelled pool) plus explicit
spread rules (topology spread constraints / pod anti-affinity) — node
count and eligibility are two different requirements, and only the
Kubernetes scheduler enforces both together correctly.

⚠️ **Correct pod spread still does not make PostgreSQL, Redis, or a
user's active connection highly available on its own.** The single
PostgreSQL instance and single Redis above (§ below) remain single
points of failure regardless of how the application pods are spread;
session affinity (used for stateful routing) is itself evidence that
some paths need sticky routing. Node-loss survival is a target to test
against, not a guarantee this document can assert — and the pilot does
not deliver overall HA while these single points of failure remain.

</details>
