# Adapter Role Mapping Contract

## Purpose
Define generation-time mapping from canonical role contracts to runtime adapter candidates.

## Scope
- Applies only to runtime adapter generation and adapter drift validation tasks.
- Does not apply to normal execution tasks.

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
| `.ai/agents/product-spec.md` | `product-spec` | PRD/specification owner for scope, stories, criteria, and constraints. | Spec artifacts only. | Broad feature implementation ownership by default. | Runs before architecture and implementation for Medium/Large tasks. |
| `.ai/agents/architect.md` | `architect` | Technical design owner for architecture boundaries, contracts, and risks. | Technical-design artifacts only. | Broad feature implementation ownership by default. | Runs after product spec and before implementation. |
| `.ai/agents/backend.md` | `backend` | Backend implementation worker for API/service/data tasks under parent constraints. | Backend modules, service layer, backend tests. | Frontend UI modules, deployment/ops files, unrelated domains. | Can run in parallel with frontend/tester after planning outputs exist. |
| `.ai/agents/frontend.md` | `frontend` | Frontend implementation worker for UI/state/integration tasks under parent constraints. | Frontend modules, UI tests, component state logic. | Backend persistence/migration files, deployment/ops files, unrelated domains. | Can run in parallel with backend/tester after planning outputs exist. |
| `.ai/agents/qa.md` | `tester` | Validation worker focused on test coverage, repro steps, and risk checks. | Test files, test fixtures, validation artifacts. | Product logic outside test scope unless parent reassigns. | Can run in parallel with implementation after planning outputs exist. |
| `.ai/agents/code-review.md` | `reviewer` | Review worker focused on correctness, maintainability, risk, and regressions. | Review notes and scoped remediation when assigned. | Broad feature implementation ownership by default. | Runs after implementation/test outputs exist. |
| `.ai/agents/docs.md` | `docs` | Documentation worker for feature docs, setup notes, handoffs, and changelog/readme updates when assigned. | Documentation artifacts only. | Broad feature implementation ownership by default. | Runs after reviewer and before parent final validation. |

## Mapping Constraints
- Adapter names must be stable and unique.
- Mappings must include explicit source-role pointers.
- Mappings must preserve policy compatibility and parent gate enforcement.
- Mapping changes require drift-aware regeneration for affected adapters.

## Planning and Parallelism Rules
- `project-manager`, `product-spec`, and `architect` are default planning agents.
- Planning agents are not broad writers.
- Planning agents run before implementation agents for Medium/Large tasks.
- `backend`, `frontend`, and `tester` may run in parallel only after planning outputs are complete.
- `reviewer` runs after implementation/test outputs exist.
- `docs` runs after reviewer and before parent final validation.
