# Adapter Role Mapping Contract

## Purpose
Define generation-time mapping from canonical role contracts to runtime adapter candidates.

## Scope
- Applies only to runtime adapter generation and adapter drift validation tasks.
- Does not apply to normal execution tasks.
- Includes Codex runtime orchestration bootstrap exposure for main-session routing.

## Canonical Rule
- `.ai/agents/*` remains canonical.
- Adapter mappings are derivative and selective.
- Not every role file must generate an adapter.

## Default Generated Roles
Generate adapters by default for:
- project-manager (`.ai/agents/project-manager.md`)
- product-spec (`.ai/agents/product-spec.md`)
- architect (`.ai/agents/architect.md`)
- backend (`.ai/agents/backend.md`)
- frontend (`.ai/agents/frontend.md`)
- tester from qa (`.ai/agents/qa.md`)
- reviewer from code-review (`.ai/agents/code-review.md`)
- docs (`.ai/agents/docs.md`)

## Opt-In Roles
Do not generate by default:
- security (generate only when explicitly requested)
- devops (generate only when explicitly requested)
- database and other specialized adapters (generate only when explicitly requested)

Rationale:
- Cross-cutting planning roles are required for Medium/Large planning output.
- Security and operations roles are risk/need driven and not universal.

## Mapping Table

| Source Role File | Adapter Name | Adapter Description | Default Write Scope | Default Forbidden Scope | Delegation Notes |
|---|---|---|---|---|---|
| `.ai/agents/project-manager.md` | `project-manager` | Planning coordinator for classification, milestones, and handoffs before implementation. | Planning artifacts only. | Broad feature implementation ownership by default. | Runs before implementation for Medium/Large tasks. |
| `.ai/agents/product-spec.md` | `product-spec` | PRD/specification owner for scope, stories, criteria, constraints, and final consolidated spec. | Spec artifacts only. | Broad feature implementation ownership by default. | Runs after project-manager; drives user clarification loop and consolidated spec approval before architecture/implementation. |
| `.ai/agents/architect.md` | `architect` | Technical design owner for architecture boundaries, contracts, and executor handoff package. | Technical-design artifacts only. | Broad feature implementation ownership by default. | Runs only after approved consolidated spec and before any implementation executors. |
| `.ai/agents/backend.md` | `backend` | Backend implementation worker for API/service/data tasks under parent constraints. | Backend modules, service layer, backend tests. | Frontend UI modules, deployment/ops files, unrelated domains. | Runs only after approved consolidated spec + architect handoff; may run in parallel with frontend when ownership boundaries are explicit. |
| `.ai/agents/frontend.md` | `frontend` | Frontend implementation worker for UI/state/integration tasks under parent constraints. | Frontend modules, UI tests, component state logic. | Backend persistence/migration files, deployment/ops files, unrelated domains. | Runs only after approved consolidated spec + architect handoff; may run in parallel with backend when ownership boundaries are explicit. |
| `.ai/agents/qa.md` | `tester` | Validation worker focused on test coverage, repro steps, and risk checks against approved spec. | Test files, test fixtures, validation artifacts. | Product logic outside test scope unless parent reassigns. | Runs after implementation outputs are available; validates against approved consolidated spec and acceptance criteria. |
| `.ai/agents/code-review.md` | `reviewer` | Review worker focused on correctness, maintainability, risk, and regressions. | Review notes and scoped remediation when assigned. | Broad feature implementation ownership by default. | Runs after tester outputs are available. |
| `.ai/agents/docs.md` | `docs` | Documentation worker for feature docs, setup notes, handoffs, changelog/readme updates, and run-specific docs report artifacts when assigned. | Documentation artifacts only. | Broad feature implementation ownership by default. | Runs last after implementation, tester, and reviewer outputs; before parent final validation; writes a run-specific docs report artifact for each docs run. |

## Mapping Constraints
- Adapter names must be stable and unique.
- Mappings must include explicit source-role pointers.
- Mappings must preserve policy compatibility and parent gate enforcement.
- Mapping changes require drift-aware regeneration for affected adapters.
- Parent/orchestrator runtime exposure rule:
  - Do not map parent/orchestrator as a normal child implementation adapter role.
  - Expose parent orchestration behavior through Codex runtime bootstrap artifact `.ai/runtimes/codex/orchestration-bootstrap.md`.
- Display-name strategy for generated adapters:
  - Use nickname candidates from `.ai/runtimes/codex/nickname-strategy.md`.
  - Runtime-facing display format should be `<nickname> [<canonical-role>]`.
  - Canonical role remains authoritative for routing/reporting.
- Relevant implementation agent requirement for code-changing runs:
  - frontend-only: `frontend` and/or framework specialist (for example `vue`, `react`),
  - backend-only: `backend` and/or framework specialist (for example `fastapi`, `laravel`, `node-express`),
  - cross-layer: both `backend` and `frontend`,
  - docs-only: `docs`,
  - test-only: `tester`.

## Planning and Parallelism Rules
- Discovery/spec-first order for Medium/Large delegated runs:
  - `project-manager` -> `product-spec` -> approved consolidated spec -> `architect`.
- Planning agents are not broad writers.
- `backend` and `frontend` must not run before approved consolidated spec and architect handoff.
- `backend` and `frontend` may run in parallel only after ownership boundaries are explicit.
- If implementation discovers requirement ambiguity, escalate through parent/`product-spec` (no direct user questioning by implementation children).
- `tester` runs after implementation and validates against approved consolidated spec + acceptance criteria.
- `reviewer` runs after tester.
- `docs` runs last before parent final validation.
- When `docs` runs, persist a run-specific docs report artifact (including remediation/final-rerun and non-merge-ready run status when applicable).
- For Tiny/Small code-changing runs where `docs` is skipped for efficiency, parent/main must still persist `/artifacts/docs/<run-id>-run-report.md`.
