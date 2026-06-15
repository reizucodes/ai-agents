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
- If runtime guidance conflicts with canonical contracts, canonical contracts win.

## Scope
This bootstrap is for normal OpenCode startup only.

Main-session rule:
- The main session is not an implementation agent.
- The primary OpenCode agent must remain orchestration-only.
- When a suitable OpenCode worker exists, delegation is required.
- If the required worker path is unavailable, disclose the gap and obtain explicit approval before parent-only fallback unless the user explicitly says `no subagent` or `main only`.

Do not load OpenCode adapter/build contracts during normal startup:
- `.ai/runtimes/opencode/adapter.md`
- `.ai/runtimes/opencode/adapter-schema.md`
- `.ai/runtimes/opencode/agent-mapping.md`
- `.ai/runtimes/opencode/workflow-mapping.md`
- `.ai/runtimes/opencode/command-mapping.md`
- `.ai/runtimes/opencode/validation.md`
- `.ai/workflows/build-opencode-agents.md`

Load those files only for adapter generation/validation/review tasks.
