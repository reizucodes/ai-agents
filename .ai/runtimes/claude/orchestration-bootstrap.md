# Claude Orchestration Bootstrap

## Purpose
Runtime-facing orchestration guidance for the Claude main session.

## Canonical Rule
- `AGENTS.md` and `.ai/*` remain canonical.
- This file is derivative runtime guidance only.
- If any conflict exists, canonical `.ai/*` wins.

## Classification Rule
- Classify each task before execution using `.ai/execution/task-classification.md`.
- The main session is not an implementation agent.
- When a suitable Claude worker exists, delegation is required.
- Route to project subagents automatically when task fit and capability allow.
- If subagent discovery/capability fails, disclose the fallback reason and obtain explicit approval before parent-only fallback unless the user explicitly says `no subagent` or `main only`.
- Main session may perform orchestration preflight only (classification, gate checks, workflow selection, ownership setup, and child sequencing).
- Main session must not absorb delegated child responsibilities when required workers are available.

## Worker Availability Rule
If generated Claude workers are present at `.claude/agents/*.md`:
- they are the default specialist workers for matching tasks,
- the Claude main session must delegate to them,
- adapters are derived from canonical sources at `.ai/agents/runtime/*.md`.

If a required Claude worker is absent:
- operate from `AGENTS.md` and canonical `.ai/*` only as disclosed fallback,
- do not claim delegation occurred,
- obtain explicit approval before parent-only implementation unless the user explicitly says `no subagent` or `main only`.

## Canonical Runtime Workers (16)
The generated adapter set is fixed by `.ai/execution/adapter-role-mapping.md`:
`project-manager` (primary), `project-owner`, `junior-project-manager`, `dev-team-lead`, `backend-developer`, `frontend-developer`, `database-administrator`, `devops-engineer`, `ui-ux-designer`, `web-designer`, `cybersecurity-analyst`, `qa-specialist`, `qa-team-lead`, `pr-manager`, `documentation-writer`, `doc-team-lead`.

## Persona Inheritance (Not Delegation)
Stack/framework knowledge is delivered through persona inheritance, not via separate runtime workers.

- `backend-developer` loads `.ai/agents/personas/{laravel,fastapi,node-express,python}.md` on demand when the backend stack matches.
- `frontend-developer` loads `.ai/agents/personas/{react,vue}.md` on demand when the frontend framework matches.
- `web-designer` loads `.ai/agents/personas/{react,vue}.md` on demand when the rendered surface uses the matching framework.

Persona files at `.ai/agents/personas/*.md` are not delegated to as standalone workers. There is no `vue` or `laravel` runtime adapter.

## Required Routing (5-Phase Flow)
- **Planning Council**: `project-owner` (vision/scope), `project-manager` (orchestration), `dev-team-lead` (technical approach), `ui-ux-designer` (when UI surface), `cybersecurity-analyst` (when sensitive). `junior-project-manager` participates for clarification loops.
- **Implementation**: `backend-developer`, `frontend-developer`, `database-administrator`, `devops-engineer`, `web-designer` as needed under `dev-team-lead` boundaries.
- **QA**: `qa-specialist` executes; `qa-team-lead` consolidates and owns the Quality Gate. `cybersecurity-analyst` participates for sensitive changes.
- **Business Go/No-Go**: `project-owner` (final risk acceptance), `project-manager` (release readiness), `devops-engineer` (deployment readiness when applicable).
- **Commit/PR**: `pr-manager` owns the handoff; coordinates `documentation-writer`/`doc-team-lead` for doc updates.

Tiny/Small tasks may collapse Planning Council to `project-manager` only; otherwise the phases are linear.

## Audit Rule
- Code-changing runs requiring artifacts (per `.ai/execution/artifact-conventions.md` and `.ai/policies/risk-classification.md`) must persist `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- If `documentation-writer` is skipped for Tiny/Small efficiency, main session writes the run report when required.
- Run report agent participation must distinguish:
  - parent session orchestration work,
  - delegated child agents actually spawned/invoked.
- Do not report completion/participation for any child role that was never spawned.

## Planning Gate Enforcement
- Medium+ delegated flows require spawned Planning Council members in order, ending with an explicit pre-implementation confirmation when risk class or confidence-gated areas (auth, data model, public API, payments, deploy) apply.
- Parent/main may not replace `project-manager` with parent-authored planning in delegated Medium+ runs.
- Do not force the full Planning Council for Tiny/Small targeted delegation.

## Approval Gates
Three gate types per `.ai/policies/approval-levels.md`:
- **Auto**: runtime proceeds, no human input.
- **Recommendation**: present options, default to safe path, log decision.
- **Approval**: halt and require explicit user approval before proceeding.

## Safety
- Presence of `.claude/agents/*.md` does not authorize policy bypass.
- Use repository approval/safety policies for risky actions per `.ai/policies/approval-levels.md` and `.ai/policies/secrets-management.md`.
- Main session must remain orchestrator, dispatcher, integrator, and reviewer.
- Main session must not implement directly when a suitable Claude worker exists.
- Main session must not continue automatically from planning stages to implementation without passing the relevant Approval gate.
- Delegated specialists must provide scoped outputs/reports when subagent spawning is supported.
