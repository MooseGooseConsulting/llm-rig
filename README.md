# LLM Rig

A small, forward-looking lab for serving and evaluating local language models across distinct hardware surfaces.

## Start here

- [Architecture](docs/architecture.md) — the canonical project rules and repository boundary.
- [Serving and benchmarking policy](docs/serving-and-benchmarking-policy.md) — the canonical serving, AIPerf, monitoring, persistence, and reporting contract.
- [Inference hardware](docs/inference-hardware.md) — the available hardware surfaces and the question each one needs to answer.
- [Run history](docs/run-history.md) — versioned run records and the rebuildable local DuckDB history.
- [Agent instructions](AGENTS.md) — the short behavioral contract that remains safe in agent context.

## What will be added only when it is needed

- `recipes/` — a source-pinned, verbatim upstream recipe or a named local derivation.
- `profiles/` — an executable configuration for one artifact, runtime, surface, and intent.
- `benchmarks/` — versioned AIPerf workload definitions with explicit token, request-schedule, validity, and concurrency semantics.
- `tools/` — helpers that require an explicit profile or benchmark; no hidden fallback model.

Run evidence, generated DuckDB history, model/runtime files, and secrets remain
outside Git. Human investigation reports live in Notion and retain the run IDs
and evidence states that support their claims.

This page is only an orientation map. The canonical project rules live in [Architecture](docs/architecture.md).
