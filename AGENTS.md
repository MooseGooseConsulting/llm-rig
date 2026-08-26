# Codex project router

## Authority and shared Notion knowledge

Authority is split by subject. The linked Notion pages below are authoritative for
current owner intent, product workflow, benchmark meaning, and documentation
placement. Tracked source, prompts, schemas, tests, executable configuration, and
repository policies are authoritative for behavior this repository currently
implements. Surface conflicts rather than silently duplicating or paraphrasing one
surface into the other. When implementation is requested, update stale Git behavior
and its concise repository-facing documentation; until then, distinguish intended
from implemented behavior.

Fetch the relevant stable hub before planning or documentation work:

- [**Local AI Infrastructure**](https://app.notion.com/p/3c8c4d261ef881de8396fb44f69b32b4)
  (`3c8c4d26-1ef8-81de-8396-fb44f69b32b4`) — ecosystem ownership, machines,
  infrastructure, and local versus remote-serving boundaries.
- [**LLM Rig**](https://app.notion.com/p/3c8c4d261ef8811fb645c4b81e56babb)
  (`3c8c4d26-1ef8-811f-b645-c4b81e56babb`) — model-work hub. Route durable
  knowledge to:

  - [**Mission and Operating Contract**](https://app.notion.com/p/3c8c4d261ef88182b392ecc01e6f2bfb)
    (`3c8c4d26-1ef8-8182-b392-ecc01e6f2bfb`) — owner intent, mission, boundaries,
    and opt-in scopes.
  - [**Model Bring-up and Optimization**](https://app.notion.com/p/3c8c4d261ef881deb08fc4611b400ec8)
    (`3c8c4d26-1ef8-81de-b08f-c4611b400ec8`) — artifact and recipe research,
    materialization, fit, debugging, tuning, and stop conditions.
  - [**Benchmark Standards**](https://app.notion.com/p/3c8c4d261ef881ccb6e9f40614344a64)
    (`3c8c4d26-1ef8-81cc-b6e9-f40614344a64`) — AIPerf profiles, workload meaning,
    measurement requirements, and benchmark selection.
  - [**Capacity and KV Engineering**](https://app.notion.com/p/3c8c4d261ef8816eb0c8fca1a8246ab9)
    (`3c8c4d26-1ef8-816e-b0c8-fca1a8246ab9`) — architecture-specific memory,
    KV/recurrent state, runtime allocation, and headroom.
  - [**Failure Catalog**](https://app.notion.com/p/3c8c4d261ef88107897fdf35c7a0de8a)
    (`3c8c4d26-1ef8-8107-897f-df35c7a0de8a`) — incidents, owner-reported failures,
    design inferences, provenance, and learned constraints.
  - [**Record Authoring Contracts**](https://app.notion.com/p/3c8c4d261ef881dfaa6bf203eac00952)
    (`3c8c4d26-1ef8-81df-aa6b-f203eac00952`) — required full-body contract for
    creating or materially updating an Investigation or Serving Recipe; database
    properties alone are not a complete record.
  - [**Investigations database**](https://app.notion.com/p/c40b6d6341274792a5c33cb5f8584263)
    (`c40b6d63-4127-4792-a5c3-3cb5f8584263`) — one record per investigation,
    including target, environment, attempts, measurements, failures, conclusions,
    unknowns, and raw-artifact links.
  - [**Serving Recipes database**](https://app.notion.com/p/f0c97a9ada644a46a0813f5e2fa03337)
    (`f0c97a9a-da64-4a46-a081-3f5e2fa03337`) — exact runnable configurations and
    explicitly labeled historical configuration arms, with provenance,
    hardware/runtime context, benchmark profile, measured speed and headroom,
    limitations, and lifecycle status.

Use an existing destination whenever it fits. Create a new stable page only for a
genuinely distinct durable subject after checking the hubs. Notion holds agent
knowledge, decisions, researched context, and richly explained results; Git holds
what must ship and execute; live systems prove current runtime state; raw artifact
locations—and any verified shared measurement store when one exists—retain exact
logs, telemetry, exports, and machine-queryable measurements. No shared queryable
measurement store is currently assumed. Link across these authorities instead of
creating a second paraphrased corpus. Preserve
reasoning, examples, counterexamples, provenance distinctions, and unresolved
questions; a summary is orientation, not a substitute for the complete capture.

Before taking any project action:

1. Read [docs/requirements.md](docs/requirements.md) as the implementation-facing
   requirements snapshot, then reconcile it with the current Notion Mission and LLM
   Rig pages. It is not the sole authority for owner intent. Do not silently promote
   an open interpretation into architecture.
2. Read [docs/architecture.md](docs/architecture.md) and follow its invariant rules where they do not conflict with confirmed requirements.
3. For serving, benchmarking, telemetry, monitoring, hardware, or deployment work, also read [docs/serving-and-benchmarking-policy.md](docs/serving-and-benchmarking-policy.md) and [docs/inference-hardware.md](docs/inference-hardware.md).
4. For run persistence, publication, retrieval, or comparison work, read [docs/shared-results-system-spec.md](docs/shared-results-system-spec.md) and [docs/serving-and-benchmarking-policy.md](docs/serving-and-benchmarking-policy.md).
5. For implementation sequencing, read [docs/implementation-plan.md](docs/implementation-plan.md) and execute its current vertical slice rather than adding another policy layer.

Do not proceed on an unconfigured project task until the required document(s) above have been read.

This file is intentionally a narrow Codex entry point. Owner requirements and
open product questions are maintained in the linked Notion knowledge surface;
[docs/requirements.md](docs/requirements.md) records the repository-facing snapshot
needed to implement them.
Confirmed invariant project rules live in [docs/architecture.md](docs/architecture.md); the serving system's
operational rules live in
[docs/serving-and-benchmarking-policy.md](docs/serving-and-benchmarking-policy.md),
with publication transport defined in
[docs/shared-results-system-spec.md](docs/shared-results-system-spec.md). Do not duplicate or diverge from
these authorities.
