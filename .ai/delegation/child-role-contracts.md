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
- For Medium/Large tasks, planning roles (`project-manager`, `product-spec`, `architect`) run before implementation roles.
- Implementation roles (`backend`, `frontend`, `tester`) may run in parallel only after planning outputs exist.
- `reviewer` runs after implementation/test outputs are available.
- `docs` runs after `reviewer` and before parent final validation.

## Child Scope Rules
- Each child receives explicit goals, scope boundaries, and handoff requirements.
- Planning roles are not broad writers by default.
- `docs` owns feature documentation, setup notes, handoff notes, and changelog/readme updates when assigned.
- Children should avoid changing files outside assigned ownership.
- Children should report assumptions, risks, and incomplete checks.

## Non-goals
- This file does not create or spawn child agents.
- This file does not define runtime adapter formats.
