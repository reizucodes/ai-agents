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
  - `project-manager` -> `product-spec` -> approved spec -> `architect` -> `backend`/`frontend` -> `tester` -> `reviewer` -> `docs`
- Remediation/final rerun:
  - targeted agents -> `tester` -> `reviewer` -> `docs` when in scope

## Audit Rule
- Every code-changing run must persist:
  - `/artifacts/docs/<run-id>-run-report.md`
- If `docs` is skipped for Tiny/Small efficiency, main session writes the run report.

## Safety
- Presence of `.claude/agents/*.md` does not authorize policy bypass.
- Use repository approval/safety policies for risky actions.
