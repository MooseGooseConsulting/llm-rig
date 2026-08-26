# Run history

## Direction

Preserve every executed run as a versioned `run.json` plus a bounded evidence
manifest outside Git. Build a local DuckDB history from those durable records.
The database is a query index that can be rebuilt; it is not a prerequisite for
finishing a run and it does not replace the evidence.

The durable unit is one executed run: the exact source/recipe and local command, the actual hardware/runtime state, the workload, the resulting measurements, and immutable pointers to raw output. A table supports the questions that matter:

- What has actually been run for this artifact, runtime, and hardware surface?
- Which configuration measured best for this named workload?
- What changed between two runs?
- Which raw logs and telemetry support a result?

## Planned first shape

Begin with the smallest relational shape that answers current serving and
benchmark questions. Use fixed columns for stable identity and comparison axes,
plus structured fields for backend-specific measurements that have not earned a
permanent column.

| Field group | Minimum contents |
| --- | --- |
| Identity | Run ID, timestamp, host/surface, operator or automation identity, status |
| Artifact | Model/artifact identifier, immutable revision or file hash, quantization |
| Runtime | Runtime name, version/build, driver/CUDA facts actually observed |
| Recipe | Source URL/revision, published/reproduced/local-variant status, exact launch command, named local deltas |
| Workload | AIPerf profile and dataset identity, input/output semantics, request schedule, concurrency, context/cache contract |
| Result | TTFT, prefill, decode, aggregate throughput, VRAM, power, correctness/quality outcome where measured |
| Evidence | Relative evidence paths, content hashes, parser/schema versions, telemetry coverage, and validity state |
| Structured fields | Configuration and measurements that do not merit permanent columns yet |

## Storage path

DuckDB is the immediate relational history because it works without a cluster,
can ingest AIPerf-shaped analytical data, and can be rebuilt from `run.json`
records. The generated database lives outside Git alongside other local state.

Each host may spool completed run directories locally. An operator imports or
collects those directories into the queryable DuckDB history. Idempotency is by
run ID and evidence hash. A future shared PostgreSQL service may receive a
replica, but it is not the authority or a dependency until its lifecycle,
availability, ingestion, and backup path are actually implemented and verified.

Do not store every high-frequency telemetry sample as a generic measurement
row. Retain raw AIPerf telemetry in the run directory and index useful
aggregates, sample coverage, and evidence pointers. If a future query cannot be
answered, add the smallest relation needed. A graph is justified only by a
concrete relationship query that the relational history and Notion cannot
answer well.

Notion is the human-facing report surface. Investigation and Run pages are
generated from normalized data and retain run IDs and evidence states; they are
not independent measurement authority. See
[Serving and benchmarking policy](serving-and-benchmarking-policy.md).
