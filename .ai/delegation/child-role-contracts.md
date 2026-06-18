# Child Role Contract Mapping

## Purpose
Map canonical role contracts to delegated child responsibilities during delegated execution. Generation-time adapter mapping lives in `.ai/execution/adapter-role-mapping.md`.

## Canonical Source Rule
- `.ai/agents/runtime/*.md` is the canonical runtime worker source (16 fixed roles).
- `.ai/agents/personas/*.md` are inheritable skill/persona docs, not child role definitions.
- Child contracts must not redefine canonical policy constraints.

## Canonical Runtime Roles (16)
- `backend-developer`
- `cybersecurity-analyst`
- `database-administrator`
- `dev-team-lead`
- `devops-engineer`
- `doc-team-lead`
- `documentation-writer`
- `frontend-developer`
- `junior-project-manager`
- `pr-manager`
- `project-manager`
- `project-owner`
- `qa-specialist`
- `qa-team-lead`
- `ui-ux-designer`
- `web-designer`

## Baseline Delegated Role Mapping
Each child resolves to its canonical contract at `.ai/agents/runtime/<role>.md`. Examples:
- `backend-developer` child → `.ai/agents/runtime/backend-developer.md`
- `frontend-developer` child → `.ai/agents/runtime/frontend-developer.md`
- `qa-specialist` child → `.ai/agents/runtime/qa-specialist.md`
- `qa-team-lead` child → `.ai/agents/runtime/qa-team-lead.md`
- `pr-manager` child → `.ai/agents/runtime/pr-manager.md`
- `documentation-writer` child → `.ai/agents/runtime/documentation-writer.md`
- `dev-team-lead` child → `.ai/agents/runtime/dev-team-lead.md`
- `project-manager` child → `.ai/agents/runtime/project-manager.md`
- `project-owner` child → `.ai/agents/runtime/project-owner.md`

## Persona Inheritance
When a runtime worker operates on a codebase that matches a persona (e.g. `backend-developer` on a Laravel project), the worker inherits the relevant `.ai/agents/personas/<stack>.md` content. Persona files are not spawnable child roles; they augment the runtime worker's prompt only when task detection matches.

## Conditional Role Participation
- `cybersecurity-analyst` — when risk/policy or auth/data boundary work requires it.
- `devops-engineer` — when deployment/ops impact requires it.
- `database-administrator` — when schema/data-model work is in scope.
- `ui-ux-designer` — when UI/UX surface design is in scope.
- `web-designer` — when visual/web-styling implementation is in scope.
- `junior-project-manager` — when requirements clarification is needed without full Planning Council.

## Planning and Implementation Ordering
- **Planning Council** runs first for Medium/Major: `project-owner` (vision/scope), `project-manager` (orchestration), `dev-team-lead` (technical approach), `ui-ux-designer` (if UI surface), `cybersecurity-analyst` (if sensitive).
- **Proposal gate** — parent halts after planning and before implementation; required proposal artifacts must exist as repository files (see `.ai/execution/artifact-conventions.md`); explicit user approval required before any implementation role runs.
- **Implementation roles** — `backend-developer`, `frontend-developer`, `database-administrator`, `devops-engineer`, `web-designer` operate within `dev-team-lead` boundaries; may run in parallel only when ownership boundaries are explicit.
- Tiny/Small targeted runs do not require `SPEC_APPROVED` / `ARCHITECTURE_READY` unless risk/scope escalates.
- **QA phase** — `qa-specialist` executes; `qa-team-lead` consolidates and owns the Quality Gate.
- **Business go/no-go** — `project-owner` (risk acceptance) + `project-manager` (release readiness).
- **PR/release handoff** — `pr-manager` owns commit message, PR body, change summary; `documentation-writer` / `doc-team-lead` update docs.
- Run-report rule: when `documentation-writer` is assigned, it writes `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`. For Tiny/Small code-changing runs where it is skipped, parent/main writes it.

## Child Scope Rules
- Each child receives explicit goals, scope boundaries, ownership, and handoff requirements.
- Implementation children must not ask the user directly; requirement questions escalate through parent and `project-owner` / `junior-project-manager`.
- Children should avoid changing files outside assigned ownership.
- Children must report assumptions, risks, and incomplete checks.

## Non-goals
- This file does not create or spawn child agents.
- This file does not define runtime adapter formats.
- Sequential role simulation must not be labeled as delegated child execution.
