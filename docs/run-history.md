# Run publication and authority

## One shared history

There is one canonical normalized measurement history: the Notion Run database.
There is one configured shared artifact store for immutable AIPerf exports and
bounded diagnostics. A host-local `run.json` and raw-output directory are a
crash-safe publication spool, not another history database.

The repository does not currently implement the publisher, Notion schema,
publication endpoint, or artifact store. Therefore this document is a contract,
not a description of a live data path.

The durable unit is one published Run: the exact source/profile and command, the
actual hardware/runtime state, the workload, the resulting measurements, and
immutable pointers to raw output. The shared registry must answer:

- What has actually been run for this artifact, runtime, and hardware surface?
- Which configuration measured best for this named workload?
- What changed between two runs?
- Which raw logs and telemetry support a result?

## Planned Run shape

Begin with the smallest shared shape that answers current serving and benchmark
questions. Use stable Notion properties for identity and comparison axes, plus
structured artifact files for backend-specific measurements that have not
earned a permanent field.

| Field group | Minimum contents |
| --- | --- |
| Identity | Run ID, timestamp, host/surface, operator or automation identity, publication and execution status |
| Artifact | Model/artifact identifier, immutable revision or file hash, quantization |
| Runtime | Runtime name, version/build, driver/CUDA facts actually observed |
| Profile and provenance | Source URL/revision, exact launch command, and every local delta |
| Workload | AIPerf definition and dataset identity, input/output semantics, request schedule, concurrency, context/cache contract |
| Result | TTFT, prefill, decode, aggregate throughput, VRAM, power, correctness/quality outcome where measured |
| Validation | Benchmark-definition version, validator result, completed and failed requests, actual token counts, timing window, and execution state |
| Raw artifacts | Shared artifact URI, content hashes, parser/schema versions, and telemetry coverage |

## Producer and transport

The benchmark runner produces only the staged run directory. A separate
publisher validates it and sends it over outbound HTTPS to the LLM Rig
publication endpoint. The endpoint stores the artifact bundle, upserts the
Notion Run by stable run ID and artifact hash, and returns a publication receipt.
This is the same path for a workstation, Blood Arrow, a Vast.ai guest, or another
remote host; no host needs access to a database file on another machine.

If the endpoint is unavailable, the run remains `pending-publication` in its
local spool. Retrying is idempotent. The host must not delete the staged artifacts
until it has a durable receipt for both the Notion Run and shared artifacts. A
locally successful benchmark is not a shared or reported result until then.

Execution failure and publication failure are different. A failed benchmark
process may be published with execution status `failed` and its validator result
recorded. A failed publication attempt creates no shared Run record and leaves
the local stage `pending-publication`.

Do not store every high-frequency telemetry sample as a Notion row. Retain raw
AIPerf telemetry in the shared artifact bundle and publish useful aggregates,
sample coverage, and artifact pointers. If future analytical queries outgrow
Notion, adopt one central SQL authority through an explicit migration; do not
create a database per host or an ungoverned replica.

Notion Investigations are human synthesis over related canonical Run records.
They do not contain independently typed measurement claims. See
[Serving and benchmarking policy](serving-and-benchmarking-policy.md).
