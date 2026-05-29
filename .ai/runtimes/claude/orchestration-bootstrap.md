# Claude Orchestration Bootstrap

## Purpose
Runtime-facing orchestration guidance for Claude main session when project-level subagents are available.

## Canonical Rule
- `AGENTS.md` and `.ai/*` remain canonical.
- This file is derivative runtime guidance only.
- If any conflict exists, canonical `.ai/*` wins.

## Classification Rule
- Classify each task before execution.
- Route to project subagents automatically when task fit and capability allow.
- If subagent discovery/capability fails, fall back to sequential execution and disclose the fallback reason.
<<<<<<< HEAD
- Main session may perform orchestration preflight only (classification, gate checks, workflow selection, ownership setup, and child sequencing).
- Main session must not absorb delegated child responsibilities when delegated routing is selected and required children are available.
=======
- For `targeted`/`delegated` fallback:
  - request user approval before sequential role simulation,
  - do not claim delegation occurred.
>>>>>>> 8ab6bbf (feat(orchestration): enforce proposal gates and delegated workflow contracts)

## Adapter Absence Rule
If Claude adapters are absent:
- operate directly from `AGENTS.md` and canonical `.ai/*`
- do not assume generated adapters exist

Generated adapters are optional runtime accelerators.
Canonical instructions remain fully usable without generated adapters.

## Required Routing
- Full project review with artifact output:
  - `reviewer` -> `docs`
- Review plus validation:
  - `reviewer` -> `tester` as needed -> `docs`
- Tiny/Small frontend change:
  - `frontend` and/or framework specialist -> audit report
- Tiny/Small backend change:
  - `backend` and/or framework specialist -> audit report
- Medium/Large feature:
  - `project-manager` -> `product-spec` -> `architect` -> proposal artifact validation + consolidated proposal package using `.ai/templates/proposal-review-package.md` + explicit approval -> `backend`/`frontend` -> `tester` -> `reviewer` -> `docs`
- Remediation/final rerun:
  - targeted agents -> `tester` -> `reviewer` -> `docs` when in scope

## Audit Rule
- Every code-changing run must persist:
  - `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`
- If `docs` is skipped for Tiny/Small efficiency, main session writes the run report.
- Run report agent participation must distinguish:
  - parent session orchestration work,
  - delegated child agents actually spawned/invoked.
- Do not report completion/participation for any child role that was never spawned.

## Planning Gate Enforcement
- Medium/Large delegated flow requires spawned planning children in this order:
  - `project-manager` first,
  - `product-spec` second,
  - `architect` after approved consolidated spec.
- Parent/main may not replace `project-manager` with parent-authored planning in delegated Medium/Large runs.
- Do not force `project-manager` for Tiny/Small targeted delegation.

## Safety
- Presence of `.claude/agents/*.md` does not authorize policy bypass.
- Use repository approval/safety policies for risky actions.
- Main session must remain orchestrator in `targeted` and `delegated`.
- Main session must not implement directly in `delegated`.
- Main session must not continue automatically from planning stages to implementation without passing Proposal Approval Gate.
- In `targeted`/`delegated`, delegated specialists must provide scoped outputs/reports when subagent spawning is supported.
