---
title: "Qwen3.8 27B Q5 unified-KV, concurrency 2"
tested_at_utc: "2026-08-25T15:49:20.1922861Z/2026-08-25T15:56:51.1627502Z"
written_at_utc: "2026-08-26T09:58:36Z"
written_by_model: "gpt-5.6-sol"
reviewed_by_model: "gpt-5.6-sol"
reviewed_by_reasoning_effort: "medium"
run_id: "qwen38-q5-unified-c2-b10622-20260825"
---

# Qwen3.8 27B Q5 unified-KV, concurrency 2

This is one llama.cpp run. It is not the report for the entire Qwen3.8
investigation, which also included Escha W2 on its custom SGLang runtime.

## The numbers

| What | Measured result |
|---|---:|
| Configured context | 262,144-token unified pool |
| Concurrency | 2 simultaneous requests / 2 slots |
| Prompt throughput, short requests | 327.8 and 327.5 tokens/s |
| Generation throughput, short requests | 71.9 and 71.7 tokens/s per stream |
| Aggregate generation throughput, short requests | 143.7 tokens/s |
| Maximum whole-device VRAM used | 30,289 MiB / 32,607 MiB |

The configured context is one shared 262,144-token pool. It is not 262,144
tokens reserved for each slot, and this report does not invent an "effective
context per slot" value.

## What ran

| Item | Exact value |
|---|---|
| Model artifact | `Qwen3.8-27B-UD-Q5_K_XL.gguf` from `unsloth/Qwen3.8-27B-GGUF` revision `27af057ecb382ddfea5d12837360a8980560e3ed` |
| Weight format / quantization | `UD-Q5_K_XL` filename variant; retained GGUF metadata identifies file type 17 / Q5_K_M base |
| Runtime | llama.cpp build 10622, commit `3737e4137` |
| Main KV cache | Q5_0 K / Q5_0 V, unified |
| Draft KV cache | F16 default |
| Workload | Two concurrent 316-token tool-call prompts followed by an attempted pair of 98,311-token prompts |

## Result

Both short structured tool calls completed correctly. The two long requests
were cancelled after 450.97 seconds with neither complete; the run therefore
did not qualify 98,311-token concurrent operation.

## Most important limitation

The 30,289 MiB peak is whole-device physical VRAM use. The contemporaneous
WDDM process-attribution capture failed, so this run cannot split that peak
between inference and the desktop renderer.

## Why the KV cache was Q5

The model artifact variant and Q5 KV quantization are independent choices. Q5
KV was selected because the requested experiment specified a 262,144-token
shared pool, concurrency 2, and an initial target near 29.5--30.0 GiB total
device use. It was not selected because a Q5 model should automatically use a
Q5 KV cache.

The measured peak left 2,318 MiB free, so this candidate was less aggressive
than the owner's later clarified target of roughly 1 GiB or less free. A more
aggressive KV configuration would require another measured run; it is not a
conclusion from this result.

## Exact configuration and raw evidence

- Exact server arguments: `D:\AIModels\runtime-state\qwen38-q5-unified-c2-b10622-20260825\server-argv.json`
- Short-request results: `D:\AIModels\runtime-state\qwen38-q5-unified-c2-b10622-20260825\short-c2-results.json`
- Stack, context, VRAM, and outcome: `D:\AIModels\runtime-state\qwen38-q5-unified-c2-b10622-20260825\stack.json`
- Retained GGUF file-type metadata: `D:\AIModels\runtime-state\qwen38-speed-bench-b9842-20260824\manager-models-before.json`
- AIPerf artifacts and telemetry: `D:\AIModels\runtime-state\qwen38-q5-unified-c2-b10622-20260825\aiperf-artifacts-long`
