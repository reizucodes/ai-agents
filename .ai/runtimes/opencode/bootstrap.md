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
Before classification or delegation preflight, check these two conditions. When either applies, skip the routing contract below entirely.

- **Exception A — Framework-Native Context:** `.ai/.framework-root` exists at repo root. → Main session acts directly. Delegation suspended. Native subagents allowed.
- **Exception B — Build-Bootstrap Operation:** Prompt matches `build claude agents`, `build codex agents`, or `build opencode agents` (exactly or as leading phrase); or invokes `.ai/workflows/build-<runtime>-agents.md`. → Main session executes build workflow directly. Delegation preflight and adapter presence check skipped.

See `.ai/execution/modes.md` for full exception definitions.

## Prompt Routing Contract (Unconditional)
Every prompt follows this routing contract exactly:
1. **Classify** — main session classifies the task (size, risk, code-changing or not).
2. **Spawn** — main session spawns `project-manager` for Small/Medium/Major work, Q&A, or analysis; or the matching specialist(s) directly for Tiny work (single specialist for single-surface; two disjoint specialists for multi-surface). Main session stops here.
3. **PM owns everything after** — `project-manager` convenes the Planning Council, decides which agents to spawn, sequences all phases, and enforces gates. Main session does not sequence council members, does not plan beyond classification, and does not implement.
4. **If the required adapter is absent** — main session discloses the missing adapter by name, halts, and awaits explicit user instruction (`no subagent` / `main only`). Main session never implements inline as a silent fallback.

Main-session rule:
- The main session is not an implementation agent.
- Delegation is unconditional for all work — adapter absence requires disclosure and halt, not inline fallback.
- The primary OpenCode agent (`project-manager`) must remain orchestration-only.
- `opencode.json` `default_agent: project-manager` enforces this for OpenCode — every prompt routes to PM automatically.

Do not load OpenCode adapter/build contracts during normal startup:
- `.ai/runtimes/opencode/adapter.md`
- `.ai/runtimes/opencode/adapter-schema.md`
- `.ai/runtimes/opencode/agent-mapping.md`
- `.ai/runtimes/opencode/workflow-mapping.md`
- `.ai/runtimes/opencode/command-mapping.md`
- `.ai/runtimes/opencode/validation.md`
- `.ai/workflows/build-opencode-agents.md`

Load those files only for adapter generation/validation/review tasks.
