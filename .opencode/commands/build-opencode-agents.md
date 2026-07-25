---
description: Build the 16 OpenCode markdown agent adapters (.opencode/agents/*.md) from canonical .ai runtime role contracts. Natural invocation "build opencode agents".
---

Follow the canonical workflow `.ai/workflows/build-opencode-agents.md`. Do not duplicate its logic here — read and execute it.

Exception B (build-bootstrap) applies: the main session executes this workflow directly, with no delegation preflight and no adapter-presence check (see `.ai/execution/modes.md`).

Source-repository guard is mandatory and enforced by the workflow: if `.ai/.framework-root` exists at repo root, refuse generation by default and return the required refusal message + consumer/test flow unless the user gives the exact override `override: generate opencode agents in source repo`.

This command is startup-safe: no generated-agent frontmatter, no plugin or dependency setup. Never run package managers (`npm`, `pnpm`, `yarn`, `bun`) or create dependency/lockfile artifacts.

Generate exactly the 16 canonical adapters at `.opencode/agents/<role>.md` per `.ai/execution/adapter-role-mapping.md`, using `.ai/runtimes/opencode/adapter-schema.md`. Never generate adapters from `.ai/agents/personas/*`. Write the run report to `.ai/reports/opencode-adapter-run-report.md`.
