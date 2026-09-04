# Run playbook

This is the only document you must read before a bring-up, serving check,
benchmark, or tuning run. Everything else is linked from here and read only when
the step you are on needs it.

## When this applies

Use this playbook for a bring-up of a new artifact, runtime build, or hardware
surface; a serving check against an endpoint; a benchmark run; or tuning an
already-serving configuration.

Do not use it for policy, specification, requirements, or plan edits. Those
follow the authority routing in [AGENTS.md](../AGENTS.md).

The default local surface is the RTX 5090 workstation: 32 GB, and it also drives
the Windows desktop, so its usable VRAM is measured with the display active
rather than assumed. A result must be valid there and must stay valid when the
same work is repeated later on another surface (RTX 3090, Blood Arrow, N5, or a
rented GPU). The surfaces are inventoried in
[Inference hardware](inference-hardware.md).

## Step 1 - Declare the run

Write a run manifest before touching the GPU. Its shape is
[Run manifest](run-manifest.md).

Every axis is fixed in the manifest: artifact, runtime build, hardware surface,
context, KV cache type, batch and ubatch, parallel slots, speculation, workload,
and concurrency. Nothing that changes the result is left to be picked at the
command line.

Changing an axis later means a new declared row with its rationale, never a
silent retry with different settings.

## Step 2 - Choose parameters (recipe ladder)

The hardware surface is an explicit input to this step, not a detail discovered
later. Work down the rungs and stop at the first that applies.

### Rung 1: exact recipe

A Serving Recipes row with Status `Current` or `Best Known` for the same
artifact, runtime family, and hardware surface. Use its settings verbatim and
record the recipe ID as provenance. Do not improve it in passing.

### Rung 2: nearest recipes

No exact row, but a row exists for the same model family, or for the same
runtime on the same hardware. Split its settings in two:

- Carry the surface-independent settings: KV cache type, flash attention,
  speculation mode, quantization family, tokenizer and chat template.
- Recompute the surface-bound settings for the target surface: context length,
  batch and ubatch, memory utilization, offload split, and parallel slots. These
  come from the capacity calculation, not from the source row. On the 5090 the
  display's VRAM overhead is measured, not assumed.

The capacity calculation itself lives on the Notion Capacity and KV
Engineering page (`3c8c4d26-1ef8-816e-b0c8-fca1a8246ab9`); this repository
does not carry the formula. Fetch that page when you reach this step. The
resource-labelling rules for the resulting numbers are in
[Serving and benchmarking policy](serving-and-benchmarking-policy.md).

Name every source recipe in the manifest's `provenance.source_recipes`.

### Rung 3: no local recipe

Start from the upstream or artifact-author recipe plus an architecture
calculation read from artifact metadata (GGUF header, `config.json`), following
these steps from the Notion Model Bring-up and Optimization page
(`3c8c4d26-1ef8-81de-b08f-c4611b400ec8`):

> 1. Gather the closest applicable upstream, artifact-author, practitioner, and
>    measured local recipes.
> 2. Separate transferable settings from hardware-, runtime-, model-, and
>    workload-specific settings.
> 3. Inspect what current upstream deliberately omits; an omission is not
>    permission to invent a flag.
> 4. Calculate capacity and resource implications.
> 5. Produce one informed aggressive candidate—not a timid generic default and
>    not a large factorial matrix.
> 6. Record the lineage of every important choice and mark sourced facts
>    separately from local inference.

### Rules for every rung

- Produce one informed aggressive candidate. Not a timid default, and not a
  factorial matrix.
- Mark sourced facts separately from local inference in the manifest.
- Never infer architecture from a model name. Read the artifact metadata.

As of 2026-09-04 every Serving Recipes row has Status `Historical smoke`, so
rung 1 never fires yet. Rung 2 applies to Qwen3.8 and Tiel work on the RTX 3090
and Threadripper surfaces, including carrying their surface-independent settings
to the 5090; everything else is rung 3. The rows live in the Notion Serving
Recipes database (`f0c97a9a-da64-4a46-a081-3f5e2fa03337`) and are not copied
into this repository.

## Step 3 - Preflight before GPU time

Compute weights, KV cache, and activation/scratch memory at the declared context
and batch. Probe the live surface for free VRAM, host memory, free ports, and
standing services. Compare the two.

A predicted failure is not a result. If the calculation says the configuration
fits, run it. If it says it does not fit, record the calculation and declare a
different row; do not run it to watch it fail, and do not quietly shrink the
context instead. Shared-surface safety and resource-labelling rules are in
[Serving and benchmarking policy](serving-and-benchmarking-policy.md).

## Step 4 - Execute and measure

AIPerf is the one harness across llama.cpp, vLLM, and SGLang. Run the declared
warm-up, then the measured window.

Report, as the Notion Benchmark Standards page
(`3c8c4d26-1ef8-81cc-b6e9-f40614344a64`) requires:

- p50 and p90, plus p95 when the harness emits it, for TTFT, TPOT/ITL, and
  end-to-end latency;
- throughput and error rate;
- VRAM at before-load, allocated, workload peak, and after-unload, each labelled
  with how it was measured.

Validity: percentiles need enough requests to converge. Declare a fixed request
count or duration and at least three repetitions. Two short samples are smoke,
not characterization. Smoke is allowed and must be labelled smoke.

## Step 5 - Record

- The evidence bundle described in [Run manifest](run-manifest.md).
- A completed-run report using
  [the template](../templates/completed-run-report.md). It is the input to the
  Notion Investigation, not a measurement authority. Until the publication
  system in
  [Shared results system specification](shared-results-system-spec.md) exists,
  it goes under `reports/` alongside the reports that REPORTING-001 preserves;
  the policy's "no Markdown reports in the repository" rule and that transitional
  practice conflict, and the open decision in
  [Requirements](requirements.md#open-decisions) records it.
- A Notion Investigation or Serving Recipe record only when the result is
  durable enough that another agent should reuse it. Those records have a
  required body; see the Notion Record Authoring Contracts page
  (`3c8c4d26-1ef8-81df-aa6b-f203eac00952`).

A run that only proved smoke gets a report, not a recipe.

## What this playbook is not

It is not an evaluation manifest, a promotion gate, or a qualification system.
The Notion Mission and Operating Contract page
(`3c8c4d26-1ef8-8182-b392-ecc01e6f2bfb`) prohibits inventing those. This
playbook declares one experiment and records what happened to it.

## Read further only if

| You are | Read |
| --- | --- |
| editing serving or benchmark policy | [serving-and-benchmarking-policy.md](serving-and-benchmarking-policy.md) |
| working on publication or `run.json` fields | [shared-results-system-spec.md](shared-results-system-spec.md) |
| asking what a hardware surface is or can do | [inference-hardware.md](inference-hardware.md) |
| deciding what to build next | [implementation-plan.md](implementation-plan.md) |
| checking owner intent | [requirements.md](requirements.md) plus the Notion Mission page |
