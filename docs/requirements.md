# LLM Rig product requirements

This document records what LLM Rig must ship and the owner decisions that
govern that product. Architecture and implementation specifications explain how
the requirements are satisfied; they do not create additional product goals.

## Document maintenance

- Record owner requirements and corrections during the conversation in which
  they are stated.
- Keep unresolved interpretations under **Open decisions**, not in the product
  requirements.
- Remove mistaken or irrelevant draft requirements from the live requirements;
  Git retains their history.
- Keep dated intake notes in this document rather than creating a parallel
  scratch file.

## Mission candidate — awaiting owner confirmation

LLM Rig should ship a working investigation system that turns a deployment goal
or serving question into a researched and sourced recipe, executes an existing
benchmark standard against it, records what actually happened, and returns the
result and human report to one shared system. Accumulated results then determine
what is worth researching or running next.

## Product requirements

### PRODUCT-001 — Ship the complete investigation loop

The repository must ship executable software, not only policy. The working
product must cover research intake, recipe discovery or derivation, benchmark
selection and execution, result validation, publication, retrieval, and human
reporting.

### RECIPE-001 — Generate recipes from current sources and accumulated results

The product must not assume that a serving recipe already exists. Given a named
deployment goal, it must:

1. Query prior results for the model, artifact, runtime, hardware, and intended
   workload.
2. Identify the smallest missing fact that could change the configuration or
   deployment decision.
3. Inspect current primary sources, model and artifact metadata, runtime support,
   and applicable practitioner recipes.
4. Preserve an applicable published recipe with its exact source and immutable
   revision or date.
5. When no published recipe applies, produce a separately identified local
   derivation whose material choices and source inputs are explicit.

The operational procedure that implements this discovery order is the recipe
ladder in [Run playbook](run-playbook.md).

An applicable published recipe is reproduced as published before it is modified.
Do not combine flags from several sources into a synthetic published baseline.
Each later variant records the exact delta and the measured reason for trying it.

Recipe status comes from runs. Useful states include discovered, applicable,
reproduced, locally derived, qualified for a named use, rejected, and superseded.
No document or agent assertion alone makes a recipe qualified or best.

### BENCHMARK-001 — Use declared benchmark standards

Benchmarks are named, versioned definitions declared before a run. Use an
existing benchmark standard unless it cannot answer the named question. Creating
a new standard requires a documented measurement gap and an explicit definition
of the workload, tool, token semantics, schedule, completion rules, resource
measurements, and validator.

AIPerf is the default execution harness for serving benchmarks across llama.cpp,
vLLM, and SGLang. The harness and the benchmark standard are different things:
AIPerf executes a declared workload; it does not make unrelated workloads
comparable. Results are directly comparable only when the applicable benchmark
standard says they are, normally because they use the same definition and
version with compatible conditions.

### RESULTS-001 — Ship one shared result reality

The repository must ship the schemas, clients, service, and integrations that
turn locally produced run output into one shared result system. Every runner,
recipe investigator, comparison tool, and report generator must read from or
write through the same versioned contract. Do not create independent host
histories that later require manual reconciliation.

The implementation contract is [Shared results system specification](shared-results-system-spec.md).

### REPORTING-001 — Publish normalized runs and human investigations

Notion is the intended shared measurement and reporting surface. Normalized Run
records contain measured and comparison-relevant fields. Notion Investigations
explain what was tried, where and when it ran, what was learned, its limitations,
and the next useful action, using related Run records rather than independently
typed measurement values.

The completed-run report structure is input to the Notion Investigation
template. Existing completed reports must be preserved until the replacement
Notion record and report exist and have been verified.

### ARTIFACTS-001 — Retain bounded raw outputs outside Notion

Do not place high-frequency telemetry streams or arbitrary raw JSON into Notion.
The shared result system retains the benchmark exports and bounded diagnostic
output required to inspect or recompute a result. Notion Run records reference
those artifacts by stable URI and content hash.

## Open decisions

- What exact deployment-goal or serving-question input starts recipe research?
- What is the initial registry of existing benchmark standards?
- Which research sources can be queried deterministically, and when is an agent
  investigation required?
- What exact schema separates a published recipe, a locally derived recipe, and
  a host-bound executable profile?
- Which service hosts the shared-results API and artifact storage?
- What exact Notion Run properties and Investigation relations form the first
  vertical slice?
- What is the transitional reporting rule until that vertical slice is live?

## Intake log

### 2026-08-26 — Initial requirements discussion

- The repository must ship a working system rather than stop at policy.
- Recipe research and generation occur before benchmark execution and are part
  of the product.
- Prior results and a named deployment question determine what to research.
- Use AIPerf as the common serving-benchmark harness.
- Return results to one shared authority and use Notion for normalized records
  and human reports.

### 2026-08-26 — Requirements-architecture correction

- Removed control-plane terminology, host-capacity admission, requirements-log
  maintenance, and diagram styling from the numbered product requirements.
- Consolidated the duplicate benchmark requirements.
- Removed the assumption that every useful workload must be comparable.
- Declared that an existing benchmark standard is the default and that a new
  standard requires a named measurement gap.
- Replaced the abstract shared-authority statement with a requirement to ship a
  shared-results specification and implementation used by every component.

### 2026-08-26 — Implementation-sequencing correction

- Stop extending policy without an executable delivery plan.
- Show the full workflow and distinguish existing, partial, missing, next-build,
  and external components.
- Build pipelines as end-to-end vertical slices rather than completing isolated
  architecture layers.
- Use the existing Qwen run and recoverable report as the first real publication
  fixture, then execute an existing benchmark standard through the same path.

### 2026-09-04 — Reduce agent ceremony without loosening parameter selection

- The owner asked that agent work stop requiring so much preparatory reading,
  while parameter selection stays rigorous: use a recipe when one exists, and
  otherwise reason from the recipes that do exist to select optimal parameters.
- The owner's primary local target is the RTX 5090 workstation. Results must be
  valid there and must also hold when the same work is later done on other
  surfaces such as the RTX 3090, Blood Arrow, N5, or a rented GPU.
- Response: added [Run playbook](run-playbook.md) as the single required read
  before a run, added [Run manifest](run-manifest.md) for the declared
  experiment and evidence bundle, and replaced the five-document reading list in
  `AGENTS.md` with a two-tier rule (run work reads the playbook; authority edits
  read the document being changed plus architecture).
