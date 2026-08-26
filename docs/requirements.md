# LLM Rig requirements

This is the living owner-requirements record for LLM Rig. It answers two
questions before architecture or implementation is allowed to answer them:

1. What does the repository need to ship?
2. What qualities and constraints must the shipped system preserve?

When this document conflicts with architecture or policy, the conflict must be
surfaced and the downstream document reconciled. Architecture describes how
confirmed requirements are satisfied; it does not invent the product mission.

## Maintenance rule

- Record an owner requirement in this document during the conversation in
  which it is stated.
- Keep explicit owner requirements separate from agent interpretations.
- Mark unresolved interpretations and design choices as open; do not promote
  them into invariants.
- Preserve superseded requirements with a short reason rather than silently
  deleting them.
- Use one document for the requirements ledger and dated intake notes. Do not
  create a second scratch file that can diverge from this one.

## Mission candidate — awaiting owner confirmation

LLM Rig should ship an executable, engine-neutral system that can take a named
model, runtime, hardware surface, and workload; stand up or target the serving
endpoint; run a meaningful AIPerf benchmark; validate and normalize the result;
and publish the measurements, artifacts, and human-readable investigation into
one shared destination.

The repository is not presumed to be the live serving control plane. It owns
the portable product needed to produce comparable serving measurements and get
them back from any supported execution environment. The ownership boundary for
long-running production serving remains an open decision.

## Confirmed owner requirements

### R-001 — Ship a working producer, not only policy

The repository's primary deliverable is executable software that produces
useful serving-benchmark results. Documentation, schemas, and governance support
that producer; they are not substitutes for it.

### R-002 — Use one default cross-engine serving benchmark

AIPerf is the default serving benchmark across llama.cpp, vLLM, and SGLang. A
runtime-native tool is used only for a named question that the common harness
cannot answer.

### R-003 — Produce meaningful, comparable workloads

A benchmark must state the workload, token work, concurrency, duration or
request count, context semantics, resource measurements, runtime circumstances,
and completion or correctness result. A couple of unexplained short requests
are not a useful qualification.

### R-004 — Return results to one shared authority

Execution may occur on a Windows workstation, Blood Arrow, a Vast.ai guest, or
another remote host. The design must return normalized results without a shared
local database, per-host history database, or manual merging of competing
authorities. Notion is the intended shared measurement and reporting surface.

### R-005 — Preserve and publish the human report

The completed-run report structure is useful. It should be translated into a
Notion Investigation template and linked to normalized Run records, not deleted
because its first representation was Markdown. Existing completed reports must
be preserved until their Notion replacement is real and verified.

### R-006 — Keep raw output bounded and addressable

Do not put high-frequency telemetry streams or arbitrary raw JSON into Notion.
Retain the raw benchmark outputs needed to reproduce or inspect a result and
link them from the shared record.

### R-007 — Do not describe LLM Rig as a control plane by default

The repository is not assumed to control the live serving fleet. If a component
really performs control-plane work, its exact controlled resource and authority
boundary must be named.

### R-008 — Do not reduce host safety to generic capacity admission

Blood Arrow and its Vast.ai service have an existing operational topology and
ownership model. Benchmarking must inspect that live state and avoid disrupting
commercial service, but the product workflow is not organized around an
abstract `capacity available` gate. It may benchmark an existing endpoint,
launch an isolated endpoint, or defer for a concrete conflict.

### R-009 — Maintain one requirements ledger continuously

New owner requirements and corrections are written into this document as they
are stated. The dated intake log below is the scratch surface; confirmed items
are promoted into numbered requirements in the same document.

### R-010 — Use legible, information-rich diagrams

Architecture and workflow diagrams should use purposeful color and richer
styling so implemented components, missing components, authorities, and data
movement are visually distinguishable. Styling must clarify meaning rather than
decorate the page.

## Main constraint candidate — awaiting owner confirmation

The main constraint is not nominal GPU capacity. It is preserving one
comparable measurement contract across heterogeneous engines and execution
environments while reliably returning the result from remote or ephemeral
hosts to one shared reporting surface.

The current repository has an even more immediate delivery blocker: it contains
policy but no runnable benchmark definition, runner, validator, publisher,
Notion schema, or artifact transport. Until the producer path exists, the
repository cannot fulfill its mission.

## Open product decisions

- Does LLM Rig own long-running serving deployment, or only profiles, endpoint
  adapters, benchmark execution, validation, and publication?
- Where does the outbound publication endpoint run?
- What shared artifact service holds benchmark exports and bounded diagnostics?
- What exact Notion Run properties and Investigation relations are required for
  the first working vertical slice?
- What is the transitional reporting rule until the Notion path is operational?

## Intake log

### 2026-08-26

- Use color and richer styling in workflow diagrams.
- Do not call the repository a control plane without defining what it actually
  controls.
- Maintain one requirements-based governing document containing both durable
  requirements and scratch intake notes.
- Focus the repository on the thing it must actually ship.
- Remove the generic `capacity available` decision from the conceptual center
  of the workflow; represent concrete serving and execution modes instead.
- Confirm the mission and main constraint with the owner before turning the
  current interpretation into settled architecture.
