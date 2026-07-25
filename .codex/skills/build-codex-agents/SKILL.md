---
name: build-codex-agents
description: Build the 16 Codex agent adapters (.codex/agents/*.toml) from canonical .ai runtime role contracts. Trigger when the user wants to build/generate/regenerate Codex agents ("build codex agents").
---

Follow the canonical workflow `.ai/workflows/build-codex-agents.md`. Do not duplicate its logic here — read and execute it.

Exception B (build-bootstrap) applies: the main session executes this workflow directly, with no delegation preflight and no adapter-presence check (see `.ai/execution/modes.md`).

Source-repository guard is mandatory and enforced by the workflow: if `.ai/.framework-root` exists at repo root, refuse generation by default and return the required refusal message unless the user gives the exact override `override: generate codex agents in source repo`.

Generate exactly the 16 canonical adapters at `.codex/agents/<role>.toml` per `.ai/execution/adapter-role-mapping.md`, using `.ai/runtimes/codex/adapter-schema.md` and `.ai/runtimes/codex/nickname-strategy.md`. Never generate adapters from `.ai/agents/personas/*`. Write the run report to `.ai/reports/codex-adapter-run-report.md`.
