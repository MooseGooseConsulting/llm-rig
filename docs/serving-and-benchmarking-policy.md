# Serving and benchmarking policy

This policy governs ongoing model serving, serving qualification, regression
measurement, telemetry, result persistence, and human reporting in LLM Rig.
The invariant project rules remain in [Architecture](architecture.md); this
document defines how those rules apply to the serving and benchmarking system.

## Authority and independence

LLM Rig is forward-looking and cluster-independent. A legacy repository,
database address, historical run, or old service description is evidence to
inspect, not proof of current behavior. Import a legacy component only after
checking its implementation and validating it against the current hardware,
runtime, and workload.

The serving and benchmarking path must continue to work when Kubernetes,
PostgreSQL, FalkorDB, or another central service is unavailable. Those systems
may become optional integration targets; none is a prerequisite for producing,
preserving, querying, or reporting a run.

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

## Evidence classes

Every run declares one evidence class before execution. A later result may be
downgraded, but never silently upgraded.

| Class | Purpose | Decision boundary |
| --- | --- | --- |
| Smoke | Prove launch, endpoint, and response shape | Never supports a comparative performance or quality claim |
| Canary | Detect regression in an already qualified profile using a small fixed workload | Supports only comparison with the same canary definition |
| Qualification | Measure a named serving decision with adequate requests, duration, distributions, errors, resource behavior, and correctness | May support promotion for the named workload and surface |
| Endurance | Establish sustained stability and resource behavior for an ongoing serving profile | Required only when long-running operation is the decision |

A successful process exit is not sufficient evidence. Validation must consider
completed requests, finish reasons, actual input/output tokens, errors,
cancellations, warmup and measurement windows, observed concurrency, telemetry
coverage, and workload-specific correctness.

## Benchmark definitions

Each benchmark definition fixes and versions:

- AIPerf version and configuration;
- dataset locator, immutable revision, and content hash;
- tokenizer identity and revision;
- endpoint type and streaming policy;
- input and output token semantics;
- request count or duration;
- concurrency or arrival-rate behavior;
- warmup and cache-state policy;
- EOS, truncation, cancellation, and error handling;
- minimum evidence required by the declared class; and
- telemetry sources, sampling interval, and measurement window.

Configured context is capacity, not an instruction to fill the window.
Workloads come from a named serving question, published dataset, or recorded
product trace. Do not manufacture a context ladder or synthetic filler solely
because a server exposes a large context ceiling.

## Telemetry and monitoring

AIPerf telemetry is run-scoped. Start it for the benchmark, retain its bounded
output, and stop it with the benchmark. Store enough raw evidence to audit the
summary, but do not create a permanent high-frequency stream by default.

Continuous operational monitoring is a separate system. It may scrape server,
GPU, and host metrics for endpoint health, request failures, queue pressure,
memory exhaustion, utilization, and service availability. Its sampling,
retention, and alerting policy must be designed explicitly. AIPerf can provide
scheduled canary runs whose results enter benchmark history; it is not itself
the continuous monitor.

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

## Run artifacts and `run.json`

Every executed run produces a bounded run directory outside Git. Its durable
interchange record is a versioned `run.json`, accompanied by an evidence
manifest. The minimum contents are:

- stable run ID, evidence class, status, timestamps, and executor;
- serving profile identity and exact effective configuration;
- model, runtime, hardware, workload, and tokenizer provenance;
- AIPerf command/configuration and result summary;
- request counts, token work, latency/throughput measurements, finish reasons,
  errors, and correctness outcomes;
- resource summaries with measurement method and sample coverage; and
- relative evidence paths, content hashes, and parser/schema versions.

Retain full AIPerf exports and bounded diagnostic logs in the run directory.
Do not insert every telemetry sample into the relational history. Normalize
per-request results and useful aggregates while retaining hashes and paths to
their backing evidence.

An incomplete, failed, cancelled, load-only, point-sampled, inferred, or
reconstructed result remains queryable with that validity state. It must not be
promoted to qualified evidence by reporting language.

## Relational history

The immediate queryable history is a local-first DuckDB database built from
`run.json` and its normalized measurements. The database is an index over
durable run records, not the only copy of their meaning. It must be rebuildable
from the run directories and must support idempotent ingestion by run ID and
evidence hash.

The initial relational shape should cover runs, profiles, artifacts, runtimes,
workloads, request results, metric summaries, resource summaries, and evidence
files. Add tables only for concrete queries that the current shape cannot
answer.

PostgreSQL may later receive replicated run data when a reliable shared service
exists. Do not make PostgreSQL, a cluster address, or a graph database part of
the local completion contract. FalkorDB or another graph is justified only by
a concrete relationship query that DuckDB and the reporting layer cannot
answer well.

## Notion reporting

Notion is the human-facing investigation and reporting surface. An
Investigation explains the decision, evidence, interpretation, limitations,
and next useful action. Related Run entries present headline facts and retain
the corresponding run IDs and evidence states.

Generate measurement claims in Notion from normalized run data. Do not create
independent numbers in prose, copy raw telemetry streams into Notion, or treat
a narrative page as stronger evidence than its runs. Reconstructed historical
work is allowed when labeled with its source and limitations.

Human-readable Markdown reports do not live in this repository. Repository
documentation defines the system and its policy; Notion carries investigation
reports; run directories carry evidence; DuckDB supplies queryable history.

See Notion's Record Authoring Contracts page for the authoring rules referenced above: https://app.notion.com/p/3c8c4d261ef881dfaa6bf203eac00952
