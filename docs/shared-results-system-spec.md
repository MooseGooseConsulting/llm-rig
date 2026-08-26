# Shared results system specification

Status: **Draft; no implementation exists yet.**

This specification turns RESULTS-001 in [Product requirements](requirements.md)
into an implementable system. It defines one publication and query contract for
every benchmark runner, recipe investigator, comparison tool, and report
generator.

## Required outcome

A completed run produced on any supported machine can be published once,
retrieved everywhere, related to its recipe and benchmark standard, included in
a Notion Investigation, and used by later recipe research without copying data
between host-specific databases.

## Shared reality

The shared system has two coordinated storage surfaces behind one service:

1. **Notion Runs and Investigations** contain normalized identity, provenance,
   workload, status, measurements, relationships, and human interpretation.
2. **The shared artifact store** contains immutable benchmark exports and
   bounded diagnostic files addressed by URI and content hash.

The Shared Results API is the only supported publication path and the common
query boundary for software. It validates and projects the coordinated Notion
and artifact-store state as one logical result. A runner does not write directly
to Notion or maintain a historical database.

A host-local run directory is a retryable outbound spool. It is not shared
history and may be deleted only after the service returns a durable publication
receipt.

## Authoritative objects

### Benchmark standard reference

- Stable benchmark identifier and version.
- Immutable definition revision and content hash.
- Harness and harness version.
- Workload and completion-rule identity.
- Validator identity and version.

The executable benchmark definition remains versioned in Git. The shared Run
stores the immutable reference used for that execution.

### Recipe reference

- Stable recipe identifier and revision.
- Published or locally derived classification.
- Source URLs and immutable revisions or dates.
- Exact configuration or content-addressed configuration artifact.
- Parent recipe and explicit deltas when derived.
- Applicability target: model artifact, runtime, hardware, and intended use.

### Run

- Stable run ID and timestamps.
- Execution and publication status.
- Benchmark-standard and recipe references.
- Model artifact, runtime, engine, endpoint, hardware, and effective
  configuration.
- Actual request count or duration, token work, schedule, completion reasons,
  errors, and validator result.
- Measurements with their units, aggregation semantics, and measurement window.
- Resource observations with method and sample coverage.
- Artifact manifest and content hashes.

### Investigation

- Human-readable question and conclusion.
- Relations to every Run used in the conclusion.
- What was tried, where and when it ran, limitations, and next action.
- Generated measurement summaries drawn from related Run records rather than
  independently typed values.

## Publication protocol

### 1. Create publication

`POST /v1/publications`

The client submits the normalized Run candidate and artifact manifest with an
idempotency key derived from the stable run ID and manifest hash. The service
validates schema versions, benchmark and recipe references, required fields,
and content-hash syntax. It returns a publication ID and upload instructions for
artifacts that are not already present.

### 2. Upload artifacts

The client uploads the declared files to service-provided locations. The
artifact store verifies size and content hash. Undeclared files are not accepted.

### 3. Commit publication

`POST /v1/publications/{publication_id}/commit`

The service verifies the complete artifact manifest, upserts the Notion Run by
stable run ID, creates its artifact relations, and returns a signed or otherwise
verifiable receipt containing:

- run ID;
- publication ID;
- accepted schema versions;
- Notion Run identifier;
- artifact manifest URI and hash; and
- committed timestamp.

Repeating the same publication is safe and returns the same committed result.
Reusing a run ID with different normalized content or artifact hashes is a hard
conflict rather than an overwrite.

## Query protocol

At minimum, the service must support:

- `GET /v1/runs/{run_id}` for one accepted Run;
- `GET /v1/runs` filtered by model artifact, recipe, runtime, hardware,
  benchmark standard and version, execution state, validator state, and time;
- `GET /v1/recipes/{recipe_id}/runs` for recipe qualification history; and
- `GET /v1/benchmarks/{benchmark_id}/runs` for results produced under one
  declared standard.

The query response must retain benchmark and recipe identities so a consumer
does not infer comparability merely because two rows have similar metric names.

## Failure behavior

- Execution failure may be published as a failed Run with its actual output.
- Validator failure may be published but does not satisfy the benchmark
  standard.
- Upload or service failure leaves the local spool pending and retryable.
- A Notion write without a committed artifact manifest does not produce a
  publication receipt.
- A committed result is never silently replaced; corrections create an explicit
  superseding relation or a new Run.

## Components the repository must ship

1. Versioned JSON schemas for publications, Runs, receipts, recipe references,
   benchmark references, and artifact manifests.
2. A runner-side publication client with spool, resume, retry, and receipt
   verification.
3. The Shared Results API with validation, idempotency, and query endpoints.
4. A Notion schema installer/migrator and Notion read/write adapter.
5. An artifact-store adapter with hash verification.
6. A query client used by recipe research and comparison tools.
7. An Investigation publisher that fills report measurements from accepted Run
   records.
8. Contract and end-to-end tests.

## First vertical-slice acceptance

The specification is proven only when one real run can:

1. Execute on a supported host using a declared benchmark standard and recipe.
2. Survive an interrupted publication and resume without duplicating the Run.
3. Store and hash its declared artifacts.
4. Create the corresponding normalized Notion Run.
5. Generate or update a related Notion Investigation from that Run.
6. Be retrieved through the query API and used as input to a subsequent recipe
   investigation.

Passing unit tests or creating the Notion databases alone does not satisfy this
acceptance test.
