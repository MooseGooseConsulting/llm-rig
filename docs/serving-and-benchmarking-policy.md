# Serving and benchmarking policy

This policy governs ongoing model serving, serving checks and comparisons,
measurement, telemetry, result persistence, and human reporting in LLM Rig.
The invariant project rules remain in [Architecture](architecture.md); this
document defines how those rules apply to the serving and benchmarking system.

## Authority and independence

LLM Rig is forward-looking and cluster-independent. A legacy repository,
database address, historical run, or old service description is input to
inspect, not proof of current behavior. Import a legacy component only after
checking its implementation and validating it against the current hardware,
runtime, and workload.

Benchmark execution must continue when the shared publication path is
temporarily unavailable: the runner preserves a bounded local staging directory
and marks the run `pending-publication`. That local copy is not shared history and
must not be presented as a published result.

Shared querying and reporting use exactly one normalized authority: the Notion
Run database. Immutable AIPerf exports and bounded diagnostics live in exactly
one configured shared artifact store and are linked from that Run record by URI
and content hash. These are not peer histories: Notion determines which Runs and
normalized fields are accepted; the artifact store provides the immutable
artifacts used to audit or recompute them. Kubernetes, a legacy cluster address,
PostgreSQL, DuckDB, FalkorDB, and host-local Markdown are not parallel
measurement authorities.

## Implementation status

This document defines the target contract. The initial repository does not yet
implement the serving adapters, runner, publication endpoint, Notion databases,
or artifact store. Until those components exist and a run receives a publication
receipt, the repository can describe a proposed workflow but cannot claim a
centrally persisted benchmark result.

## Ongoing serving

Every served model uses an explicit profile. A profile is valid only for its
named combination of:

- model repository, immutable revision, artifact, hash, and quantization;
- runtime and immutable binary or image build;
- hardware surface and resource budget;
- operational intent;
- exact launch command and environment inputs;
- context-pool semantics, concurrency, cache type, and placement;
- OpenAI-compatible endpoint, health check, and Prometheus endpoint where
  available; and
- ownership, shutdown, and recovery behavior.

LLM Rig may support llama.cpp, vLLM, and SGLang through separate launch
adapters, but they share one serving contract. Runtime adapters are responsible
for starting, stopping, checking, and locating a server; they do not silently
change the model, workload, context, cache, concurrency, or benchmark method.

Use the supervisor appropriate to the host, such as an existing process
manager, Docker, systemd, or a Windows scheduled task. Do not require one
global orchestrator merely to make profiles look uniform.

## Standard benchmark harness

**AIPerf is the sole default serving benchmark harness across llama.cpp,
vLLM, and SGLang.** It drives the common OpenAI-compatible endpoint and supplies
one client-side measurement method across backends. The same workload,
tokenizer, streaming policy, request schedule, concurrency, warmup, and success
criteria must be used for a cross-backend comparison.

Runtime-specific Prometheus metrics may be collected during the same AIPerf
run. They explain backend behavior but do not replace the common client-visible
metrics or become falsely equivalent across runtimes.

A runtime-native benchmark is an exception, not a required arm. Use one only
when it answers a named question that AIPerf cannot answer, such as a kernel
microbenchmark, an unavailable internal counter, a runtime-specific diagnostic,
or exact reproduction of a published upstream procedure. Record the question
and why the AIPerf result is insufficient. Do not run a native tool merely
because it ships with the runtime.

Primary references:

- [AIPerf supported APIs and profiling](https://github.com/ai-dynamo/aiperf)
- [AIPerf server-metrics collection](https://github.com/ai-dynamo/aiperf/blob/main/docs/server-metrics/server-metrics.md)
- [llama-server OpenAI and metrics endpoints](https://github.com/ggml-org/llama.cpp/blob/master/tools/server/README.md)
- [vLLM OpenAI-compatible serving](https://docs.vllm.ai/en/stable/serving/openai_compatible_server.html)
- [SGLang OpenAI-compatible APIs](https://docs.sglang.ai/basic_usage/openai_api_completions.html)

## Executable benchmark definitions

The repository currently contains no runnable benchmark definition. A named
benchmark does not exist merely because a policy gives it a label. It exists
only when the repository contains all of the following and the command can run
without reconstructing missing choices from prose:

- an executable entry point and exact AIPerf invocation;
- AIPerf version and configuration;
- dataset locator, immutable revision, and content hash;
- tokenizer identity and revision;
- endpoint type and streaming policy;
- input and output token semantics;
- request count or duration;
- concurrency or arrival-rate behavior;
- warmup and cache-state policy;
- EOS, truncation, cancellation, and error handling;
- telemetry sources, sampling interval, and measurement window;
- the expected output schema; and
- a machine-executable validator.

The validator reads the produced files and records whether the run satisfied
the definition's concrete requirements. At minimum it checks completed request
count or duration, finish reasons, actual input/output tokens, errors and
cancellations, warmup and measurement windows, observed concurrency, required
resource-sample coverage, and any workload-specific correctness checks. A
successful process exit or a manually assigned label is not a benchmark result.

Configured context is capacity, not an instruction to fill the window.
Workloads come from a named serving question, published dataset, or recorded
product trace. Do not manufacture a context ladder or synthetic filler solely
because a server exposes a large context ceiling.

## Telemetry and monitoring

AIPerf telemetry is run-scoped. Start it for the benchmark, retain its bounded
output, and stop it with the benchmark. Retain the raw output needed to inspect
the summary, but do not create a permanent high-frequency stream by default.

Continuous operational monitoring is a separate system. It may scrape server,
GPU, and host metrics for endpoint health, request failures, queue pressure,
memory exhaustion, utilization, and service availability. Its sampling,
retention, and alerting policy must be designed explicitly. A future scheduler
may execute a specific versioned regression workload; AIPerf is not itself the
continuous monitor.

For each memory or resource number, record whether it is a workload-window
peak, baseline, loaded value, post-run value, point sample, process allocation,
whole-device reading, or estimate. Never label a point sample as a peak.

## Shared and commercial hardware safety

Before loading a model or starting a benchmark on a shared surface:

1. Inspect current compute, memory, endpoint, container/VM, and commercial
   workload state using the surface's live control plane.
2. Enforce the profile's resource budget and ownership boundary.
3. Skip or defer the run when safe headroom is not available.
4. Isolate benchmark ports, processes, and output paths from ongoing serving.
5. Stop only processes created by the run and verify resource recovery.

Vast.ai service on Blood Arrow has priority over exploratory benchmarking.
A benchmark must not assume that an apparently idle model endpoint means the
host, guest, or commercial allocation is idle. Recovery actions and hard power
resets belong to the hardware owner's runbook and are not ordinary benchmark
steps.

## Local staging and `run.json`

Every executed run first produces a bounded staging directory outside Git. Its
interchange and crash-recovery record is a versioned `run.json`, accompanied by
an artifact manifest. The minimum contents are:

- stable run ID, execution and publication status, timestamps, and executor;
- serving profile identity and exact effective configuration;
- model, runtime, hardware, workload, and tokenizer provenance;
- AIPerf command/configuration and result summary;
- request counts, token work, latency/throughput measurements, finish reasons,
  errors, and correctness outcomes;
- resource summaries with measurement method and sample coverage; and
- relative artifact paths, content hashes, and parser/schema versions.

Retain full AIPerf exports and bounded diagnostic logs in the staging directory
until publication is acknowledged. Do not turn every telemetry sample into a
Notion row. Publish the normalized comparison fields and useful aggregates while
retaining hashes and shared URIs for their backing artifacts.

An incomplete, failed, cancelled, load-only, point-sampled, inferred, or
reconstructed run may be published with that actual state. It cannot support a
comparison unless the named benchmark validator succeeded and the compared runs
used the same versioned definition.

Execution status and publication status are separate fields. A benchmark
process that fails may be published as an execution failure so the exact command
and output are retained. A publication attempt that fails creates no shared Run
record; its local stage remains `pending-publication` until a retry succeeds.

## Publication and shared authority

The runner does not write a historical database. A separate publisher moves a
completed staging directory into the shared system through one outbound HTTPS
path suitable for a Vast.ai guest or any other remote serving host. The
publisher validates the record, stores the artifacts, idempotently upserts the
Notion Run, and returns a publication receipt. The detailed transport, retry,
and receipt contract lives in [Run publication and authority](run-history.md).

The publication endpoint's hosting provider, URL, Notion schema, artifact-store
provider, retention policy, and credential route are unselected implementation
decisions. Until they are selected and verified, a remote run can be preserved
locally but cannot get “back to us” as a shared result. Documentation must say
so rather than implying that an operator will somehow merge host-local stores.

Do not introduce DuckDB, SQLite, or one database per runner. If the normalized
history eventually needs SQL that Notion cannot provide, make a deliberate
migration to one centrally hosted PostgreSQL authority behind the same
publication API. Do not dual-write two canonical histories. Disposable analyst
exports and caches are allowed only when they are clearly rebuildable and never
described as authority.

## Notion authority and reporting

The Notion Run database is the canonical normalized measurement registry. An
Investigation explains the decision, run outputs, interpretation, limitations,
and next useful action. Related Run entries carry the comparison fields, run
IDs, execution state, benchmark-definition version, validator result, and
immutable artifact links that support the Investigation.

The publisher generates Run fields from the validated staged record; agents do
not type independent measurement numbers into Investigation prose. Do not copy
raw telemetry streams into Notion. Reconstructed historical work is allowed
when labeled with its source and limitations.

Human-readable Markdown reports do not live in this repository. Repository
documentation defines the system and its policy; Notion carries canonical Run
records and Investigation reports; the shared artifact store carries immutable
raw output; local run directories are temporary staging and recovery state.
