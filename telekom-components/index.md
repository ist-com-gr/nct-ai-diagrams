# NCT-AI / Telekom Components

[Open interactive index](index.html) | [Source overview](https://github.com/ist-com-gr/NCT-AI/blob/889128d85af7dcf478c9651e89df05d1288ec825/design/Components/NCT-AI_Components_Overview_Telekom_2026-09-17.md)

Nine English-only views derived from the 2026-09-17 overview, including the complete section 3 communication map. These are document-based diagrams, not new infrastructure measurements or compliance certification.

| View | Topic | Interactive HTML | Markdown |
|---|---|---|---|
| 01 | Component map | [Inventory](01-component-map.html) | [Text diagram](01-component-map.md) |
| 02 | Experience communication | [Selected calls](02-experience-communication.html) | [Text diagram](02-experience-communication.md) |
| 03 | Integration paths | [Selected calls](03-integration-paths.html) | [Text diagram](03-integration-paths.md) |
| 04 | Temporal and application budget | [Pilot proposal](04-temporal-budget.html) | [Text diagram](04-temporal-budget.md) |
| 05 | State and availability | [Pilot target](05-state-availability.html) | [Text diagram](05-state-availability.md) |
| 06 | Installation decisions | [Decision dependencies](06-installation-decisions.html) | [Text diagram](06-installation-decisions.md) |
| 07 | NIS2 use-case controls | [Governance model](07-nis2-controls.html) | [Text diagram](07-nis2-controls.md) |
| 08 | GDPR data scope | [Governance model](08-gdpr-data-scope.html) | [Text diagram](08-gdpr-data-scope.md) |
| 09 | Complete component communications | [Complete communication map](09-complete-communications.html) | [Text diagram](09-complete-communications.md) |

## Scope

The original overview and the Q1-Q30 series are unchanged. Source assertions about residency, portability and missing artifact storage are explicitly qualified in view 06. All viewer controls and authored explanations are English. No server or CDN is required.

The public mirror includes these HTML/Markdown pairs and their selected source excerpts. Source documents, specs and verification evidence remain private; missing copied references are rendered as text.

## Rebuild and Verify

```sh
node design/Prerequisites/diagram-lab/telekom-components/tools/build.mjs
ARCHIFY_CLI=/path/to/archify/bin/archify.mjs node design/Prerequisites/diagram-lab/telekom-components/tools/build.mjs --validate
ARCHIFY_CLI=/path/to/archify/bin/archify.mjs node design/Prerequisites/diagram-lab/telekom-components/tools/build.mjs --deliver
ARCHIFY_CLI=/path/to/archify/bin/archify.mjs node design/Prerequisites/diagram-lab/telekom-components/tools/browser-check.mjs
node design/Prerequisites/diagram-lab/telekom-components/tools/verify.mjs --browser
```

Specifications are authored in specs/. The catalog owns explanations; generation does not overwrite diagram geometry. Receipts and screenshots live in .verification/. Delivery, browser checks and perceptual review are separate evidence. See [verification summary](https://github.com/ist-com-gr/NCT-AI/blob/889128d85af7dcf478c9651e89df05d1288ec825/design/Prerequisites/diagram-lab/telekom-components/.verification/summary.json) after completion.
