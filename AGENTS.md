# Codex project router

Before taking any project action:

1. Read [docs/architecture.md](docs/architecture.md) and follow its invariant rules.
2. For serving, benchmarking, telemetry, monitoring, hardware, or deployment work, also read [docs/serving-and-benchmarking-policy.md](docs/serving-and-benchmarking-policy.md) and [docs/inference-hardware.md](docs/inference-hardware.md).
3. For run persistence, publication, retrieval, or comparison work, read [docs/serving-and-benchmarking-policy.md](docs/serving-and-benchmarking-policy.md) and [docs/run-history.md](docs/run-history.md).

Do not proceed on an unconfigured project task until the required document(s) above have been read.

This file is intentionally a narrow Codex entry point. Invariant project rules
live in [docs/architecture.md](docs/architecture.md); the serving system's
operational rules live in
[docs/serving-and-benchmarking-policy.md](docs/serving-and-benchmarking-policy.md),
with publication transport defined in
[docs/run-history.md](docs/run-history.md). Do not duplicate or diverge from
these authorities.
