# Inference hardware

These are distinct serving surfaces, not interchangeable GPUs. This inventory records what is known, what each surface is for, and the next question worth answering. It intentionally does not prescribe a model or a serving configuration.

| Surface | Known shape | Intended role | Current question / acceptance condition |
| --- | --- | --- | --- |
| RTX 5090 workstation | 32 GB RTX 5090; also drives the Windows desktop | Primary interactive local serving and single-GPU benchmarking surface | For each artifact, measure physical VRAM headroom alongside display activity, then qualify the intended context/concurrency shape rather than assuming a fixed slot layout. |
| RTX 3090 | Previously passed brief Qwen3.6/Qwen3.8 endpoint checks; this repository has no runnable AIPerf workload yet | Candidate secondary local inference surface | After the runner and a versioned workload exist, establish its usable model/context/concurrency envelope and decide whether its throughput/energy trade-off makes it worth keeping online. |
| GTX 1080 Ti | Present; older-generation candidate | Targeted utility and economics experiment, not an assumed serving tier | Decide whether it has a practical role using the dedicated [GTX 1080 Ti economics](gtx-1080-ti-economics.md) evaluation. |
| N5 | Existing constrained surface with an unresolved crash/configuration problem | Compact or specialized serving surface if it can be made stable | Retest from current hardware state and exact failure output. The first goal is a stable launch and understood capacity boundary, not a guessed model fit. |
| Blood Arrow | Networked Threadripper 9985 with 512 GB RAM and two RTX PRO GPUs | High-capacity, multi-GPU, and remote-serving surface | Treat it as its own hardware/runtime lane: inspect its live topology and deployment state before selecting a model, context, parallelism, or endpoint plan. |

Concrete launch commands belong in a future source record and profile, after current upstream material and live hardware have been checked.
