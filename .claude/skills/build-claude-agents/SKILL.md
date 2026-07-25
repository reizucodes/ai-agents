---
name: build-claude-agents
description: Build the 16 Claude subagent adapters (.claude/agents/*.md) from canonical .ai runtime role contracts. Trigger when the user wants to build/generate/regenerate Claude agents ("build claude agents").
---

Follow the canonical workflow `.ai/workflows/build-claude-agents.md`. Do not duplicate its logic here — read and execute it.

Exception B (build-bootstrap) applies: the main session executes this workflow directly, with no delegation preflight and no adapter-presence check (see `.ai/execution/modes.md`).

Source-repository guard is mandatory and enforced by the workflow: if `.ai/.framework-root` exists at repo root, refuse generation by default and return the required refusal message unless the user gives the exact override `override: generate claude agents in source repo`.

Generate exactly the 16 canonical adapters at `.claude/agents/<role>.md` per `.ai/execution/adapter-role-mapping.md`, using `.ai/runtimes/claude/adapter-schema.md` and `.ai/runtimes/claude/tool-mapping.md`. Never generate adapters from `.ai/agents/personas/*`. Write the run report to `.ai/reports/claude-adapter-run-report.md`.
