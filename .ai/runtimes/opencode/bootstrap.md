# OpenCode Runtime Bootstrap

## Purpose
Startup-safe OpenCode runtime bootstrap for normal execution.

## Startup Load Model (OpenCode)
Load in this order:
1. `AGENTS.md`
2. `INDEX.md`
3. `opencode.json`
4. `.ai/runtimes/opencode/bootstrap.md`
5. `.ai/runtimes/opencode/delegation.md`

## Canonical Authority
- `AGENTS.md`, `INDEX.md`, and `.ai/*` remain canonical.
- Canonical runtime adapter sources are `.ai/agents/runtime/*.md` (exactly 16 files; see `.ai/execution/adapter-role-mapping.md`).
- `.ai/agents/personas/*.md` are inheritance-only skill docs and are NEVER runtime adapter sources.
- If runtime guidance conflicts with canonical contracts, canonical contracts win.

## Scope
This bootstrap is for normal OpenCode startup only.

## Pre-Preflight Exception Check
See `AGENTS.md § Pre-Preflight Exceptions`.

## Prompt Routing Contract
See `AGENTS.md § Prompt Routing Contract`.

OpenCode-specific: `opencode.json` `default_agent: project-manager` enforces routing — every prompt routes to PM automatically.

## Adapter/Build Files — Do Not Load on Normal Startup
Load the following only for adapter generation/validation/review tasks:
- `.ai/runtimes/opencode/adapter.md`
- `.ai/runtimes/opencode/adapter-schema.md`
- `.ai/runtimes/opencode/agent-mapping.md`
- `.ai/runtimes/opencode/workflow-mapping.md`
- `.ai/runtimes/opencode/command-mapping.md`
- `.ai/runtimes/opencode/validation.md`
- `.ai/workflows/build-opencode-agents.md`
