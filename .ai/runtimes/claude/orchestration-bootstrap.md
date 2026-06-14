# Claude Orchestration Bootstrap

## Purpose
Runtime-facing orchestration guidance for the Claude main session.

## Canonical Rule
- `AGENTS.md` and `.ai/*` remain canonical.
- This file is derivative runtime guidance only.
- If any conflict exists, canonical `.ai/*` wins.

## Classification Rule
- Classify each task before execution.
- The main session is not an implementation agent.
- When a suitable Claude worker exists, delegation is required.
- Route to project subagents automatically when task fit and capability allow.
- If subagent discovery/capability fails, disclose the fallback reason and obtain explicit approval before parent-only fallback unless the user explicitly says `no subagent` or `main only`.
- Main session may perform orchestration preflight only (classification, gate checks, workflow selection, ownership setup, and child sequencing).
- Main session must not absorb delegated child responsibilities when required workers are available.
- For specialist fallback:
  - request user approval before sequential role simulation,
  - do not claim delegation occurred.

## Worker Availability Rule
If generated Claude workers are present:
- they are the default specialist workers for matching tasks,
- the Claude main session must delegate to them.

If a required Claude worker is absent:
- operate from `AGENTS.md` and canonical `.ai/*` only as disclosed fallback,
- do not claim delegation occurred,
- obtain explicit approval before parent-only implementation unless the user explicitly says `no subagent` or `main only`.

## Required Routing
- Full project review with artifact output:
  - `reviewer` -> `documentation`
- Review plus validation:
  - `reviewer` -> `tester` as needed -> `documentation`
- Tiny/Small frontend change:
  - `frontend` and/or framework specialist -> audit report
- Tiny/Small backend change:
  - `backend` and/or framework specialist -> audit report
- Medium/Large feature:
  - `project-manager` -> `product-spec` -> `architect` -> proposal artifact validation + consolidated proposal package using `.ai/templates/proposal-review-package.md` + explicit approval -> `backend`/`frontend` -> `tester` -> `reviewer` -> `documentation`
- Remediation/final rerun:
  - targeted agents -> `tester` -> `reviewer` -> `documentation` when in scope

## Audit Rule
- Every code-changing run must persist:
  - `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`
- If `documentation` is skipped for Tiny/Small efficiency, main session writes the run report.
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
- Main session must remain orchestrator, dispatcher, integrator, and reviewer.
- Main session must not implement directly when a suitable Claude worker exists.
- Main session must not continue automatically from planning stages to implementation without passing Proposal Approval Gate.
- Delegated specialists must provide scoped outputs/reports when subagent spawning is supported.
