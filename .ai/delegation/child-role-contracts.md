# Child Role Contract Mapping

## Purpose
Map canonical role contracts to delegated child responsibilities during delegated execution.

## Canonical Source Rule
- `.ai/agents/*` remains canonical.
- Child role definitions are derived from canonical role contracts.
- Child contracts must not redefine canonical policy constraints.

## Scope Clarification
- This file defines delegated execution-time role mapping.
- Generation-time adapter mapping is defined in `.ai/execution/adapter-role-mapping.md`.

## Baseline Delegated Role Mapping
- project-manager child -> `.ai/agents/project-manager.md`
- product-spec child -> `.ai/agents/product-spec.md`
- architect child -> `.ai/agents/architect.md`
- backend child -> `.ai/agents/backend.md`
- frontend child -> `.ai/agents/frontend.md`
- tester child -> `.ai/agents/qa.md`
- reviewer child -> `.ai/agents/code-review.md`
- docs child -> `.ai/agents/docs.md`

## Conditional Role Mapping
- security child -> `.ai/agents/security.md` when risk/policy requires it.
- devops child -> `.ai/agents/devops.md` when deployment/ops impact requires it.
- stack-specialist children map to relevant framework contracts when decomposition requires it.
- Specialist children (`vue`, `react`, `laravel`, `fastapi`, `node-express`, `python`) are routable only when runtime adapters exist or the runtime can invoke the canonical role explicitly.
- If a specialist adapter is missing, parent may route through the generic owning adapter (`frontend` or `backend`) only when that generic adapter is available, the task classification allows generic ownership, and the specialist gap is disclosed.
- Missing required specialist adapters must not silently collapse into parent/main.

## Planning and Implementation Ordering Rules
- Discovery/spec roles:
  - `project-manager` then `product-spec`.
- Design/handoff role:
  - `architect` runs only after approved consolidated spec exists.
- Proposal gate:
  - parent/orchestrator must halt after planning and before implementation.
  - required proposal artifacts must exist as repository files before implementation roles run.
  - explicit user approval is required before any implementation role runs.
- Implementation roles:
  - Medium/Large delegated `backend` and `frontend` run only after architect handoff exists.
  - Tiny/Small targeted `backend`, `frontend`, and `tester` runs do not require `SPEC_APPROVED` or `ARCHITECTURE_READY` unless risk/scope escalates.
  - `backend` and `frontend` may run in parallel only when ownership boundaries are explicit.
- Validation/review/documentation roles:
  - `tester` validates against approved consolidated spec and acceptance criteria.
  - `reviewer` runs after tester output is available.
  - `docs` runs last, before parent final validation.
  - when `docs` is assigned, it must write `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
  - for Tiny/Small code-changing runs where `docs` is skipped for efficiency, parent/main writes `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.

## Child Scope Rules
- Each child receives explicit goals, scope boundaries, and handoff requirements.
- Planning roles are not broad writers by default.
- `docs` owns feature documentation, setup notes, handoff notes, and changelog/readme updates when assigned.
- `docs` report artifacts must cover initial runs, remediation runs, final reruns, and non-merge-ready outcomes when applicable.
- Required run-report content for any code-changing run:
  - run type,
  - task summary,
  - files changed,
  - agents used,
  - tests run,
  - result/status,
  - remaining risks or skipped validations,
  - report producer (`docs` or `parent/main`).
- Children should avoid changing files outside assigned ownership.
- Children should report assumptions, risks, and incomplete checks.
- Implementation children (`backend`, `frontend`) must not ask the user directly; requirement questions escalate through parent and `product-spec`.

## Non-goals
- This file does not create or spawn child agents.
- This file does not define runtime adapter formats.
- Sequential role simulation must not be labeled as delegated child execution.
