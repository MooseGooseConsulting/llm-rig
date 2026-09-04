---
title: "<plain-language run name>"
tested_at_utc: "<ISO-8601 timestamp or interval>"
written_at_utc: "<ISO-8601 timestamp>"
written_by_model: "<exact model name, for example gpt-5.6-sol>"
reviewed_by_model: "<exact model name, or not independently reviewed>"
reviewed_by_reasoning_effort: "<exact effort, if known>"
run_id: "<run identifier>"
---

# <Plain-language run name>

## The numbers

| What | Measured result |
|---|---:|
| Configured context | <total configured context tokens; say whether it is shared> |
| Concurrency | <simultaneous requests or slots actually tested> |
| Prompt throughput | <tokens/s; say per request or aggregate> |
| Generation throughput | <tokens/s per request or stream> |
| Aggregate generation throughput | <directly reported aggregate, or a value explicitly labelled as calculated from named per-stream measurements> |
| Maximum whole-device VRAM used | <MiB used / MiB total> |

If one of these was not captured, write `not captured`; do not replace it with
an estimate. Put estimates in a separate, clearly labelled section only when
they affect the next experiment.

Do not silently treat a mean per-request rate as aggregate throughput.

## What ran

| Item | Exact value |
|---|---|
| Model artifact | <filename and source revision> |
| Weight format / quantization | <exactly what the artifact metadata records> |
| Runtime | <runtime, build, and commit> |
| KV cache | <K type / V type> |
| Workload | <request shape and concurrency> |

If the raw evidence does not record a revision, quantization, runtime build,
KV type, or workload detail, write `not captured` or `unknown`. Do not fill a
field from a plan, directory name, expected recipe, or model-name shorthand.

## Result

<One plain sentence saying what completed, failed, or was cancelled.>

## Important limitation

<The most decision-relevant thing this run did not prove. Omit this section if
there is none.>

## Why this configuration was used

<State the evidence or owner instruction that selected the configuration. Do
not turn the explanation into a recommendation.>

## Exact configuration and raw evidence

- Exact command or argument file: `<path>`
- Raw measurements: `<path>`
- Stack and artifact identity: `<path>`
