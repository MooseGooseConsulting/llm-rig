# Codex project router

Before taking any project action:

1. Read [docs/architecture.md](docs/architecture.md) and follow its invariant rules.
2. For serving, benchmarking, telemetry, monitoring, hardware, deployment, or result-publication work, also read [docs/serving-and-benchmarking-policy.md](docs/serving-and-benchmarking-policy.md) and [docs/inference-hardware.md](docs/inference-hardware.md).
3. For work that retrieves, compares, or records prior runs, also read [docs/run-history.md](docs/run-history.md).

Do not proceed on an unconfigured project task until the required document(s) above have been read.

This file is intentionally a narrow Codex entry point. Invariant project rules
live in [docs/architecture.md](docs/architecture.md); the serving system's
operational rules live in
[docs/serving-and-benchmarking-policy.md](docs/serving-and-benchmarking-policy.md).
Do not duplicate or diverge from either authority.
