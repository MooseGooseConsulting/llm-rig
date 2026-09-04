# LLM Rig

A small, forward-looking lab for serving and evaluating local language models across distinct hardware surfaces.

## Current status

This repository currently defines policy and system boundaries. It does **not**
yet ship a serving CLI, AIPerf runner, publication endpoint, Notion schema, or
shared artifact store. The documentation describes the contract those pieces
must satisfy; it must not be read as proof that they are deployed.

## Start here

- [Run playbook](docs/run-playbook.md) — the only required reading before a run.
- [Run manifest](docs/run-manifest.md) — the declared experiment written before the run and the evidence bundle produced after it.
- [Requirements](docs/requirements.md) — the living owner-requirements ledger, mission candidate, delivery constraint, and open product decisions.
- [Architecture](docs/architecture.md) — the canonical project rules and repository boundary.
- [Serving and benchmarking policy](docs/serving-and-benchmarking-policy.md) — the canonical serving, AIPerf, monitoring, persistence, and reporting contract.
- [Inference hardware](docs/inference-hardware.md) — the available hardware surfaces and the question each one needs to answer.
- [Shared results system specification](docs/shared-results-system-spec.md) — the concrete API, objects, publication transaction, query contract, and first end-to-end acceptance test.
- [Implementation plan](docs/implementation-plan.md) — current system status, color-coded build map, vertical slices, and executable acceptance checks.
- [Agent instructions](AGENTS.md) — agent routing.

## What will be added only when it is needed

- `recipes/` — a source-pinned, verbatim upstream recipe or a named local derivation.
- `profiles/` — an executable configuration for one artifact, runtime, surface, and intent.
- `benchmarks/` — runnable AIPerf workloads with exact commands, datasets, schedules, and machine-checkable completion rules.
- `tools/` — helpers that require an explicit profile or benchmark; no hidden fallback model.

Local run staging, model/runtime files, and secrets remain outside Git. Once
the publication system exists, normalized Run records live in one shared Notion
database and immutable raw artifacts live in one configured shared artifact
store. A local `run.json` is a transfer and recovery envelope, not a second
history database.

This page is only an orientation map. The canonical project rules live in [Architecture](docs/architecture.md).
