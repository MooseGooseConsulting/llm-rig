# Architecture

This is the single canonical source for LLM Rig's invariant design rules. The
product requirements in [LLM Rig product requirements](requirements.md) govern
what the system must ship; this document defines the architecture used to
satisfy those requirements. Other files may describe a hardware surface, a
source, a profile, or a benchmark, but they do not redefine the requirements or
these design rules.

## Invariants

1. **No global serving defaults.** A configuration is valid only for its named artifact, runtime build, hardware surface, and operational intent.
2. **Source before tuning.** Preserve an exact current upstream recipe verbatim for its first local reproduction. A local change is a separately named variant with the exact delta and reason.
3. **One serving benchmark across backends.** Use AIPerf as the default serving benchmark for llama.cpp, vLLM, and SGLang. A runtime-native tool is an exception for a named question that AIPerf cannot answer, not a required arm or an intrinsically better measurement.
4. **Context is capacity, not an implied request.** A configured context ceiling does not reserve that amount for each request unless the runtime proves it uses fixed slots. State the cache/pool model and request shape.
5. **Measurements require retained raw output and one shared registry.** Each run first produces a versioned `run.json` and artifact manifest in a local staging directory outside Git. Publication creates one canonical normalized Run record in Notion and places its immutable raw output in one configured shared artifact store. Do not create an undefined permanent telemetry stream.
6. **No record ceremony.** Produce the staged record, bounded artifacts, and publication receipt required by the serving policy, but do not manufacture plan packets, acceptance gates, or narrative shadows of data already stored elsewhere.
7. **Legacy is input, not instruction.** Do not use an old recipe, result, or “best practice” as a starting configuration without a named reason it applies to the current source, runtime, hardware, and workload.
8. **Local state is not shared history.** A host-local run directory is a crash-safe spool until publication succeeds. Do not create a per-host DuckDB, SQLite database, Markdown report collection, or other competing measurement authority.

## Repository boundary

| Thing | Lives here? | Purpose |
| --- | --- | --- |
| Run playbook | Yes | The operational entry point for a bring-up, serving check, benchmark, or tuning run |
| Run manifest | Yes | The experiment declared before a run and the evidence bundle produced after it |
| Investigation request | Yes | Deployment goal, serving question, success condition, and the missing fact the work is intended to resolve |
| Recipe research and derivation | Yes | Current source findings, prior-result query, candidate applicability, and explicit derivation decisions |
| Surface inventory | Yes | Known hardware shape, role, and the next question for each serving surface |
| Source record | Yes, when needed | Direct source URL, revision/date, verbatim recipe, and status |
| Serving profile | Yes, when needed | Exact artifact, runtime build, hardware surface, intent, launch command, and local deltas |
| Benchmark definition | Yes, when implemented | AIPerf configuration, exact invocation, workload identity, token semantics, request schedule, output schema, and validator |
| Launcher/export helper | Yes, when needed | Explicit profile or benchmark input, validation, normalization, and output location |
| Local run staging | No | Crash-safe `run.json`, bounded raw output, and publication status until the shared authority acknowledges the run |
| Shared raw artifacts | No | One configured artifact store containing immutable AIPerf output and bounded diagnostics, addressed by URI and content hash |
| Canonical normalized run history | No | One shared Notion Run database populated idempotently by the publisher |
| Model/runtime binaries and secrets | No | Host-appropriate runtime and secret-management locations |
| Historical plans, narratives, and postmortems | No | Remain in the legacy corpus rather than entering default agent context |

## Source and run facts

A source record stores the upstream URL, immutable revision or date, and exact
published command. A local profile records every changed value and why it
changed. A run records what actually happened: process exit, completed and
failed requests, actual token counts, timing window, resource samples, output
files, and validator result. A status label or narrative cannot substitute for
those fields.

## Run composition

~~~text
deployment goal or serving question
    + query of accepted prior Runs
    + targeted current source research
    -> published recipe or explicitly derived local candidate
    -> host-bound serving profile + declared benchmark standard
    -> local staged run.json + bounded raw output outside Git
    -> Shared Results API publication
    -> shared artifact object + canonical Notion Run record
    -> related Notion Investigation
    -> next recipe or deployment decision
~~~

The repository does not provide generic SGLang, llama.cpp, vLLM, or model-family recipes. A source record records a precise published recipe when one exists. A local profile explains the concrete question it is designed to answer when one does not.

The operational policy for serving, benchmarking, monitoring, persistence, and
reporting is [Serving and benchmarking policy](serving-and-benchmarking-policy.md).
The unavailable legacy cluster and the `LocalLargeLanguageModels` repository
are not operational authority for this project; import their components only
after current implementation and live-state validation.
