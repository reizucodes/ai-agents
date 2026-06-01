# OpenCode Runtime Bootstrap

## Purpose
Startup-safe OpenCode runtime bootstrap for normal execution.

## Startup Load Model (OpenCode)
Load only:
1. `AGENTS.md`
2. `INDEX.md`
3. `opencode.json`
4. `.ai/runtimes/opencode/bootstrap.md`

## Canonical Authority
- `AGENTS.md`, `INDEX.md`, and `.ai/*` remain canonical.
- If runtime guidance conflicts with canonical contracts, canonical contracts win.

## Scope
This bootstrap is for normal OpenCode startup only.

Do not load OpenCode adapter/build contracts during normal startup:
- `.ai/runtimes/opencode/adapter.md`
- `.ai/runtimes/opencode/adapter-schema.md`
- `.ai/runtimes/opencode/agent-mapping.md`
- `.ai/runtimes/opencode/workflow-mapping.md`
- `.ai/runtimes/opencode/command-mapping.md`
- `.ai/runtimes/opencode/delegation.md`
- `.ai/runtimes/opencode/validation.md`
- `.ai/workflows/build-opencode-agents.md`

Load those files only for adapter generation/validation/review tasks.
