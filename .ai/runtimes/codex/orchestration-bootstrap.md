# Codex Orchestration Bootstrap

## Purpose
Runtime-facing orchestration instructions for the Codex main session.

This file is derived from canonical `.ai/*` contracts and exists to make routing behavior explicit in Codex runtime contexts where child adapters are discoverable but parent orchestration intent is otherwise weak.

## Canonical Rule
- `.ai/*` remains canonical.
- This bootstrap is derivative runtime guidance.
- If this file conflicts with canonical `.ai/*` contracts, follow canonical contracts.

## Main-Session Routing Rules
- Honor execution-mode input from `.ai/execution/execution-mode-input.md`:
  - `auto` | `sequential` | `targeted` | `delegated`.
- Read execution mode from runtime/launcher metadata first.
- Use prompt-body `Execution mode: ...` only as fallback when runtime metadata is unavailable.
- If no mode is provided, default to `auto`.
- Classify task before execution using `.ai/execution/task-classification.md`.
- Choose execution mode automatically from classification + capability + delegation eligibility gates.
- User does not need to explicitly request "delegated mode" for eligible tasks.
- If runtime subagent capability is unavailable, use sequential fallback and state fallback explicitly.
- For `targeted`/`delegated` with unavailable subagents:
  - report limitation,
  - request user approval before sequential role simulation fallback,
  - do not claim delegation occurred.
- For spawned agent display/traces, prefer `<nickname> [<canonical-role>]`.
- Runtime nickname alone is non-authoritative; canonical role suffix is required in display form.
- Before `targeted`/`delegated`, check:
  - runtime delegation capability,
  - Codex adapter discovery (`.codex/agents/*.toml`) when adapter-based routing is expected.

## Required Routing
- Full-project review with artifact output:
  - `reviewer` -> `docs`.
- Review + validation:
  - `reviewer` -> `tester` (when validation/coverage/test interpretation is requested) -> `docs`.
- Tiny/Small frontend code-changing run:
  - `frontend` and/or specialist (`vue`/`react`) -> audit report artifact.
- Tiny/Small backend code-changing run:
  - `backend` and/or specialist (`fastapi`/`laravel`/`node-express`) -> audit report artifact.
- Medium/Large feature:
  - `project-manager` -> `product-spec` -> `architect` -> proposal artifact validation + consolidated proposal package using `.ai/templates/proposal-review-package.md` + explicit approval -> `backend`/`frontend` -> `tester` -> `reviewer` -> `docs`.
- Remediation/final-rerun:
  - use relevant targeted agents, then `tester` -> `reviewer` -> `docs` when in scope.

## Audit/Docs Enforcement
- Every code-changing run must persist `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- For Tiny/Small efficiency cases where `docs` is skipped, parent/main writes the required run report.
- For delegated review artifact-generating runs, `docs` is required.

## Orchestrator Constraints
- `targeted`: main remains orchestrator and delegates selected specialist scopes when supported.
- `delegated`: main remains strict parent/orchestrator and must not implement directly.
- Do not collapse specialist roles into main silently.
- Do not continue automatically from planning stages to implementation without passing Proposal Approval Gate.
- In `targeted`/`delegated`, delegated specialists must provide scoped outputs/reports when subagent spawning is supported.
