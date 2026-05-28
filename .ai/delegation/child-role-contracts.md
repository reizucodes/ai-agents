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

## Planning and Implementation Ordering Rules
- Discovery/spec roles:
  - `project-manager` then `product-spec`.
- Design/handoff role:
  - `architect` runs only after approved consolidated spec exists.
- Implementation roles:
  - `backend` and `frontend` run only after architect handoff exists.
  - `backend` and `frontend` may run in parallel only when ownership boundaries are explicit.
- Validation/review/documentation roles:
  - `tester` validates against approved consolidated spec and acceptance criteria.
  - `reviewer` runs after tester output is available.
  - `docs` runs last, before parent final validation.
  - when `docs` is assigned, it must also write a run-specific docs report artifact for that run.

## Child Scope Rules
- Each child receives explicit goals, scope boundaries, and handoff requirements.
- Planning roles are not broad writers by default.
- `docs` owns feature documentation, setup notes, handoff notes, and changelog/readme updates when assigned.
- `docs` report artifacts must cover initial runs, remediation runs, final reruns, and non-merge-ready outcomes when applicable.
- Children should avoid changing files outside assigned ownership.
- Children should report assumptions, risks, and incomplete checks.
- Implementation children (`backend`, `frontend`) must not ask the user directly; requirement questions escalate through parent and `product-spec`.

## Non-goals
- This file does not create or spawn child agents.
- This file does not define runtime adapter formats.
