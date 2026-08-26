# Architecture

This is the single canonical source for LLM Rig's invariant project rules. Other files may describe a hardware surface, a source, a profile, or a benchmark, but they do not redefine these rules.

## Invariants

1. **No global serving defaults.** A configuration is valid only for its named artifact, runtime build, hardware surface, and operational intent.
2. **Source before tuning.** Preserve an exact current upstream recipe verbatim for its first local reproduction. A local change is a separately named variant with the exact delta and reason.
3. **One serving benchmark across backends.** Use AIPerf as the default serving benchmark for llama.cpp, vLLM, and SGLang. A runtime-native tool is an exception for a named question that AIPerf cannot answer, not a required arm or an intrinsically better measurement.
4. **Context is capacity, not an implied request.** A configured context ceiling does not reserve that amount for each request unless the runtime proves it uses fixed slots. State the cache/pool model and request shape.
5. **Measurements require bounded evidence and one shared registry.** Each run first produces a versioned `run.json` and evidence manifest in a local staging directory outside Git. Publication creates one canonical normalized Run record in Notion and places its immutable backing artifacts in one configured shared evidence store. Do not create an undefined permanent telemetry stream.
6. **No record ceremony.** Produce the staged record, bounded evidence, and publication receipt required by the serving policy, but do not manufacture plan packets, acceptance gates, or narrative shadows of data already stored elsewhere.
7. **Legacy is evidence, not instruction.** Do not use an old recipe, result, or “best practice” as a starting configuration without a named reason it applies to the current source, runtime, hardware, and workload.
8. **Local state is not shared history.** A host-local run directory is a crash-safe spool until publication succeeds. Do not create a per-host DuckDB, SQLite database, Markdown report collection, or other competing measurement authority.

## Repository boundary

| Thing | Lives here? | Purpose |
| --- | --- | --- |
| Surface inventory | Yes | Known hardware shape, role, and the next question for each serving surface |
| Source record | Yes, when needed | Direct source URL, revision/date, verbatim recipe, and status |
| Serving profile | Yes, when needed | Exact artifact, runtime build, hardware surface, intent, launch command, and local deltas |
| Benchmark definition | Yes, when needed | AIPerf configuration, workload identity, token semantics, request schedule, evidence class, and validity requirements |
| Launcher/export helper | Yes, when needed | Explicit profile or benchmark input, validation, normalization, and output location |
| Local run staging | No | Crash-safe `run.json`, bounded evidence, and publication status until the shared authority acknowledges the run |
| Shared raw evidence | No | One configured evidence store containing immutable AIPerf output and bounded diagnostics, addressed by URI and content hash |
| Canonical normalized run history | No | One shared Notion Run database populated idempotently by the publisher |
| Model/runtime binaries and secrets | No | Host-appropriate runtime and secret-management locations |
| Historical plans, narratives, and postmortems | No | Remain in the legacy corpus rather than entering default agent context |

## Evidence status

- **source-published** — an upstream source's claim and command; not yet our result.
- **reproduced** — the upstream command ran locally, with a linked raw artifact.
- **local variant** — a local change from a named source or baseline, with the exact delta and reason.
- **superseded** — preserved evidence for a changed artifact/runtime/hardware condition; not a live recommendation.

No status means “best.” Provenance status, shared-publication status, the
policy's Smoke/Canary/Qualification/Endurance evidence class, and execution
validity such as completed, failed, cancelled, or reconstructed are four
independent dimensions. A source, profile, or result is useful only when all
four and its source, surface, workload, and measured outcome match the decision
being made.

## Run composition

~~~text
current source evidence + named surface + serving profile + AIPerf benchmark definition
    -> local staged run.json + bounded evidence outside Git
    -> outbound HTTPS publication
    -> shared evidence object + canonical Notion Run record
    -> related Notion Investigation
~~~

The repository does not provide generic SGLang, llama.cpp, vLLM, or model-family recipes. A source record records a precise published recipe when one exists. A local profile explains the concrete question it is designed to answer when one does not.

The operational policy for serving, benchmarking, monitoring, persistence, and
reporting is [Serving and benchmarking policy](serving-and-benchmarking-policy.md).
The unavailable legacy cluster and the `LocalLargeLanguageModels` repository
are not operational authority for this project; import their components only
after current implementation and live-state validation.
