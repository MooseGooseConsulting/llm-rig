# Codex project router

Before taking any project action:

1. Read [docs/architecture.md](docs/architecture.md) and follow its invariant rules.
2. For serving, benchmarking, telemetry, monitoring, hardware, deployment, or
   result-publication work, also read
   [docs/serving-and-benchmarking-policy.md](docs/serving-and-benchmarking-policy.md) and
   [docs/inference-hardware.md](docs/inference-hardware.md).
3. For work that retrieves, compares, or records prior runs, also read
   [docs/run-history.md](docs/run-history.md).

Do not proceed on an unconfigured project task until the required document(s) above have
been read.

This file is intentionally a narrow Codex entry point. Invariant project rules
live in [docs/architecture.md](docs/architecture.md); the serving system's
operational rules live in
[docs/serving-and-benchmarking-policy.md](docs/serving-and-benchmarking-policy.md).
Do not duplicate or diverge from either authority.

## Notion authority

Notion is the live authority for decisions: standing policy, the operating model, roadmaps,
the rationale behind standing rules, and the scope of agent autonomy. There is no Markdown
mirror of Notion in this repository, and creating one is not an acceptable workaround. Read
Notion live by these stable URLs before acting on anything that needs a decision.

**Scope.** Notion decides; it does not run anything. This repository's benchmark harnesses,
result files, and serving configs stay authoritative for what they own, and a Notion page
never substitutes for an authorisation this repository's own mechanism must issue. On
disagreement: about a *decision*, Notion wins and the repository record is corrected; about
*current state*, the live system wins and both are corrected.

| Page | URL |
|---|---|
| Local AI Infrastructure (which repo and which machine owns what) | https://app.notion.com/p/3c8c4d261ef881de8396fb44f69b32b4 |
| Decisions database | https://app.notion.com/p/e23213fce2f94755af0400189541ac36 |
| LLM Rig hub | https://app.notion.com/p/3c8c4d261ef8811fb645c4b81e56babb |
| Mission and Operating Contract | https://app.notion.com/p/3c8c4d261ef88182b392ecc01e6f2bfb |
| Record Authoring Contracts | https://app.notion.com/p/3c8c4d261ef881dfaa6bf203eac00952 |
| Benchmark Standards | https://app.notion.com/p/3c8c4d261ef881ccb6e9f40614344a64 |
| Capacity and KV Engineering | https://app.notion.com/p/3c8c4d261ef8816eb0c8fca1a8246ab9 |
| Serving Recipes database | https://app.notion.com/p/f0c97a9ada644a46a0813f5e2fa03337 |
| Investigations database | https://app.notion.com/p/c40b6d6341274792a5c33cb5f8584263 |

### If Notion is unreachable

- **Governance question — STOP and report.** If the answer would come from a page above, say
  "Notion is unreachable; the governing answer is unavailable" and stop. Do not infer the
  decision from Git, from a dated file, or from memory of a previous session.
- **Pure operations — PROCEED.** Executing a documented procedure, running tests, or
  answering a code-fact question needs no Notion.
- **Never mirror.** Do not copy Notion content into this repository "so it works offline".
