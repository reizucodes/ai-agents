# Codex Orchestration Bootstrap

## Purpose
Runtime-facing orchestration instructions for the Codex main session.

This file is derived from canonical `.ai/*` contracts and exists to make routing behavior explicit in Codex runtime contexts.

## Canonical Rule
- `.ai/*` remains canonical.
- This bootstrap is derivative runtime guidance.
- If this file conflicts with canonical `.ai/*` contracts, follow canonical contracts.

## Prompt Routing Contract (Unconditional)
Every prompt follows this routing contract exactly:
1. **Classify** — main session classifies the task (size, risk, code-changing or not).
2. **Spawn** — main session spawns `project-manager` for Small/Medium/Major work, Q&A, or analysis; or the matching specialist(s) directly for Tiny work (single specialist for single-surface; two disjoint specialists for multi-surface). Main session stops here.
3. **PM owns everything after** — `project-manager` convenes the Planning Council, decides which agents to spawn, sequences all phases, and enforces gates. Main session does not sequence council members, does not plan beyond classification, and does not implement.
4. **If the required adapter is absent** — main session discloses the missing adapter by name, halts, and awaits explicit user instruction (`no subagent` / `main only`). Main session never implements inline as a silent fallback.

## Main-Session Routing Rules
- The main session is not an implementation agent.
- Delegation is unconditional for all work — adapter absence requires disclosure and halt, not inline fallback.
- Classify task before execution using `.ai/execution/task-classification.md`.
- User does not need to explicitly request delegation for matching specialist work.
- For specialist routing with unavailable subagents:
  - disclose the missing adapter by name,
  - halt and await explicit user instruction (`no subagent` / `main only`),
  - do not implement inline,
  - do not claim delegation occurred.
- For spawned agent display/traces, prefer `<nickname> [<canonical-role>]`.
- Runtime nickname alone is non-authoritative; canonical role suffix is required in display form.
- Before specialist delegation, check:
  - runtime delegation capability,
  - exact required Codex role adapters (`.codex/agents/<role>.toml`).
- Required preflight output:
  - `runtime_spawn_supported`
  - `classification`
  - `required_roles`
  - `available_adapters`
  - `missing_adapters`
  - `delegation_decision`
  - `fallback_action`

## Canonical Runtime Workers (16)
The generated adapter set is fixed by `.ai/execution/adapter-role-mapping.md`:
`project-manager` (primary), `project-owner`, `junior-project-manager`, `dev-team-lead`, `backend-developer`, `frontend-developer`, `database-administrator`, `devops-engineer`, `ui-ux-designer`, `web-designer`, `cybersecurity-analyst`, `qa-specialist`, `qa-team-lead`, `pr-manager`, `documentation-writer`, `doc-team-lead`.

## Persona Inheritance (Not Delegation)
Stack knowledge is delivered through persona inheritance, not separate runtime workers.
- `backend-developer` loads `.ai/agents/personas/{laravel,fastapi,node-express,python}.md` on demand.
- `frontend-developer` loads `.ai/agents/personas/{react,vue}.md` on demand.
- `web-designer` loads `.ai/agents/personas/{react,vue}.md` on demand.

There is no `vue`, `react`, `laravel`, `fastapi`, `node-express`, or `python` runtime adapter. Those names live only under `.ai/agents/personas/*.md`.

## Required Routing (5-Phase Flow)
- **Planning Council**: `project-owner` (vision/scope), `project-manager` (orchestration), `dev-team-lead` (technical approach), `ui-ux-designer` (when UI surface), `cybersecurity-analyst` (when sensitive). `junior-project-manager` participates for clarification loops.
- **Implementation**: `backend-developer`, `frontend-developer`, `database-administrator`, `devops-engineer`, `web-designer` as needed under `dev-team-lead` boundaries.
- **QA**: `qa-specialist` executes; `qa-team-lead` consolidates and owns the Quality Gate.
- **Business Go/No-Go**: `project-owner` (final risk acceptance), `project-manager` (release readiness), `devops-engineer` (deployment readiness when applicable).
- **Commit/PR**: `pr-manager` owns the handoff; coordinates `documentation-writer`/`doc-team-lead`.

Tiny/Small tasks may collapse Planning Council to `project-manager` only.

## Approval Gates
Three gate types per `.ai/policies/approval-levels.md`:
- **Auto**: runtime proceeds, no human input.
- **Recommendation**: present options, default to safe path, log decision.
- **Approval**: halt and require explicit user approval before proceeding.

## Audit/Docs Enforcement
- Code-changing runs that require artifacts (per `.ai/execution/artifact-conventions.md` and `.ai/policies/risk-classification.md`) must persist `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- If `documentation-writer` is skipped for Tiny work, the run report requirement is waived. If a run report is required, it must be written by a delegated agent (`documentation-writer` or `doc-team-lead`) — the main session must not write files.

## Orchestrator Constraints
- main remains orchestrator and delegates selected specialist scopes when supported.
- main must not implement directly when a suitable Codex worker exists.
- Do not collapse specialist roles into main silently.
- Missing required adapters must be disclosed and must not silently collapse into parent/main.
- Do not continue automatically from planning stages to implementation without passing the relevant Approval gate.
- Delegated specialists must provide scoped outputs/reports when subagent spawning is supported.
