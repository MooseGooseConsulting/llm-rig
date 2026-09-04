# Run manifest

A run manifest declares one experiment before it consumes GPU time. The evidence
bundle records what that experiment actually produced. The document that uses
both is [Run playbook](run-playbook.md).

## Declared experiment (before the run)

Write this before touching the GPU. Every axis that changes the result appears
here, so no execution choice hides in a runbook, an agent prompt, a runner
constant, or a handwritten command.

The example is concrete on purpose. It takes the Bloodarrow 3090 Tiel IQ4_XS 16K
MTP smoke row as its rung 2 source and retargets it at the RTX 5090 workstation.

```yaml
schema_version: run-manifest/v1alpha1

run:
  id: rtx5090-tiel-iq4xs-2026-09-04
  hypothesis: Tiel IQ4_XS serves interactively on the 5090 beside the desktop.
  named_uncertainty: Usable VRAM on the 5090 while the display is active.

artifact:
  provider: huggingface
  repo_id: <owner>/<repo>
  revision: <immutable-revision>
  files: [Tiel-Coder-35B-A3B-MTP-UD-IQ4_XS.gguf]
  integrity: {algorithm: sha256, expected: <digest>}

runtime:
  engine: llama.cpp
  build: server-cuda13-b10524
  backend: cuda

hardware:
  surface: rtx-5090-workstation
  required_probes: [gpu_memory_free, display_vram_overhead, host_memory, ports,
                    standing_services]

serving:
  context_tokens: <recomputed for 5090>
  kv: {k: q8_0, v: q8_0}
  batch: <recomputed for 5090>
  ubatch: <recomputed for 5090>
  parallel_slots: 1
  flash_attention: true
  speculation: {mode: mtp}

provenance:
  recipe_rung: 2
  source_recipes:
    - "Bloodarrow 3090 · Tiel IQ4_XS · 16K · MTP smoke"
  inference_notes: >
    KV cache type, flash attention, MTP mode, quantization family and slot count
    carry from the 3090 row. Its 16384-token context and its batch sizes do not
    transfer: the 5090 has a different VRAM budget and also drives the desktop,
    so context, batch and ubatch are recomputed from the capacity calculation
    for this surface.

workload:
  harness: aiperf
  profile: <declared benchmark standard id and version>
  input_tokens: <declared>
  output_tokens: <declared>
  concurrency: 1
  warmups: 1
  repetitions: 3
  sampling: {temperature: 0, seed: 0}

preflight:
  architecture_source: artifact_metadata
  calculation_recorded: true

execution:
  transient_attempt_limit: 1
  parameter_fallbacks: forbidden
```

### Derived rows

Any changed axis produces a new manifest, not an edit to this one. The new
manifest names its parent and the evidence that justified the change:

```yaml
derived_from: rtx5090-tiel-iq4xs-2026-09-04
decision:
  evidence_attempts: [<attempt-id>]
  rationale: <hypothesis supported by observed evidence>
  authored_by: <human or agent identity>
  recorded_at: <timestamp>
```

A transient infrastructure retry of the same declared row is not a derived row.

Static fallback ladders are forbidden. An agent chooses the next row by
reasoning from the previous attempt's telemetry and writes that reasoning down
before launching, rather than stepping through a preset list of smaller
contexts.

## Evidence bundle (after the run)

One run directory per attempt, containing:

- `run.json`, the normalized result for that run;
- `aiperf-config.yaml`, the exact harness configuration used;
- the AIPerf profile output;
- server metrics collected during the measured window;
- GPU and host telemetry;
- `commands.json`, every command actually executed;
- `manifest.json`, hashes and provenance for every file above plus the declared
  run manifest;
- `logs/`.

The fields inside `run.json` are defined by
[Shared results system specification](shared-results-system-spec.md) and are not
restated here.

Every attempt produces a bundle and a row in the report table, including
preflight-blocked, failed, and stopped attempts. A prediction is not a
measurement, and a run table that drops its failures is not the run table.

## Not a gate

This manifest is not an evaluation manifest, a promotion gate, or a
qualification system. The Notion Mission and Operating Contract page
(`3c8c4d26-1ef8-8182-b392-ecc01e6f2bfb`) prohibits inventing those. It declares
one experiment and records what happened to it.

## Open questions

- The exact grammar for `run.id`.
- Whether a retry of the same declared row is a child attempt or a new row.
- Whether the manifest file is checked into Git or stays in the run directory.
