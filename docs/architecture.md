# Architecture

This is the single canonical source for LLM Rig's invariant project rules. Other files may describe a hardware surface, a source, a profile, or a benchmark, but they do not redefine these rules.

## Invariants

1. **No global serving defaults.** A configuration is valid only for its named artifact, runtime build, hardware surface, and operational intent.
2. **Source before tuning.** Preserve an exact current upstream recipe verbatim for its first local reproduction. A local change is a separately named variant with the exact delta and reason.
3. **One serving benchmark across backends.** Use AIPerf as the default serving benchmark for llama.cpp, vLLM, and SGLang. A runtime-native tool is an exception for a named question that AIPerf cannot answer, not a required arm or an intrinsically better measurement.
4. **Context is capacity, not an implied request.** A configured context ceiling does not reserve that amount for each request unless the runtime proves it uses fixed slots. State the cache/pool model and request shape.
5. **Measurements require bounded evidence, not prose authority.** Each run produces a versioned `run.json` and evidence manifest outside Git. Retain the AIPerf output and diagnostics required to audit the result; do not create an undefined permanent telemetry stream.
6. **No record ceremony.** Produce the `run.json` and bounded evidence required by the serving policy, but do not manufacture plan packets, acceptance gates, or narrative shadows of data already stored elsewhere.
7. **Legacy is evidence, not instruction.** Do not use an old recipe, result, or “best practice” as a starting configuration without a named reason it applies to the current source, runtime, hardware, and workload.

## Repository boundary

| Thing | Lives here? | Purpose |
| --- | --- | --- |
| Surface inventory | Yes | Known hardware shape, role, and the next question for each serving surface |
| Source record | Yes, when needed | Direct source URL, revision/date, verbatim recipe, and status |
| Serving profile | Yes, when needed | Exact artifact, runtime build, hardware surface, intent, launch command, and local deltas |
| Benchmark definition | Yes, when needed | AIPerf configuration, workload identity, token semantics, request schedule, evidence class, and validity requirements |
| Launcher/export helper | Yes, when needed | Explicit profile or benchmark input, validation, normalization, and output location |
| Raw logs, telemetry, and model/runtime binaries | No | Bounded external run directories and model/runtime locations |
| Queryable measurement history | Generated, not committed | A rebuildable local DuckDB index over versioned run records |
| Historical plans, narratives, and postmortems | No | Remain in the legacy corpus rather than entering default agent context |

## Evidence status

- **published** — a source's claim and command; not yet our result.
- **reproduced** — the published command ran locally, with a linked raw artifact.
- **local variant** — a local change from a named source or baseline, with the exact delta and reason.
- **superseded** — preserved evidence for a changed artifact/runtime/hardware condition; not a live recommendation.

No status means “best.” A source, profile, or result is useful only when its source, surface, workload, and measured outcome match the decision being made.

## Run composition

~~~text
current source evidence + named surface + serving profile + AIPerf benchmark definition
    -> versioned run record + bounded evidence outside Git
    -> rebuildable DuckDB history + Notion investigation projection
~~~

The repository does not provide generic SGLang, llama.cpp, vLLM, or model-family recipes. A source record records a precise published recipe when one exists. A local profile explains the concrete question it is designed to answer when one does not.

The operational policy for serving, benchmarking, monitoring, persistence, and
reporting is [Serving and benchmarking policy](serving-and-benchmarking-policy.md).
The unavailable legacy cluster and the `LocalLargeLanguageModels` repository
are not operational authority for this project; import their components only
after current implementation and live-state validation.
