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

LLM Rig should ship an investigation system that can take a deployment goal or
serving question; inspect prior measured results and current upstream sources;
identify the smallest decision-relevant knowledge gaps; produce a sourced
candidate recipe; stand up or target the serving endpoint; run a meaningful
AIPerf benchmark; validate and normalize the result; and publish the
measurements, artifacts, and human-readable investigation into one shared
destination. Each completed result becomes input to the next recipe decision.

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

### R-011 — Recipe discovery is part of the shipped product

LLM Rig must not assume that an executable recipe already exists. It must
support the research and reasoning path that discovers a published recipe or
derives a local candidate from current model metadata, runtime support, target
hardware, intended workload, and prior measurements.

### R-012 — Research begins with a named decision and coverage gap

Research is selected by comparing the requested deployment or serving decision
with the accumulated run history and current known sources. Investigate the
smallest missing fact that could change the candidate recipe. Do not produce an
open-ended literature search, context ladder, or configuration matrix merely
because many parameters exist.

### R-013 — Preserve published baselines and derived variants separately

When an applicable published recipe exists, preserve its source, immutable
revision or date, and first reproduction exactly as published. Do not combine
flags from several sources into a synthetic baseline. A local recipe is a
separately named derivation whose every meaningful delta has a reason tied to
the target hardware, artifact, runtime, workload, or a measured failure.

### R-014 — Recipe status comes from runs

A recipe moves through explicit states such as discovered, applicable,
reproduced, locally derived, qualified for a named workload, rejected, or
superseded. A document or agent assertion cannot mark a recipe best or qualified;
that status must be derived from validated runs on the named surface and
workload.

## Main constraint candidate — awaiting owner confirmation

The primary constraint is closing the full knowledge loop without allowing an
agent to invent a recipe or run an arbitrary experiment: deployment question ->
prior-result query -> targeted current research -> sourced candidate -> measured
run -> shared result -> next decision. Cross-engine comparability and reliable
publication from remote or ephemeral hosts are constraints inside that larger
loop, not the starting point.

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
- What executable interface expresses a deployment question and the comparison
  or success condition it is meant to resolve?
- Which sources are searched automatically for each model/runtime family, and
  which require an explicitly delegated research investigation?
- What is the machine-readable boundary between a published source recipe, a
  locally derived recipe, and a host-bound serving profile?

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
- Recipe discovery and generation are part of the product; a completed serving
  profile cannot be assumed as the first input.
- Use the requested deployment decision and missing coverage in prior runs to
  decide what to research.
- Preserve an applicable published recipe exactly for its first reproduction;
  keep locally derived variants and their reasons separate.
