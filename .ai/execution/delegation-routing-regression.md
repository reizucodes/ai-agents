# Delegation Routing Regression Coverage

## Purpose
Capture routing regressions that must remain unambiguous across `INDEX.md`, `.ai/execution/*`, `.ai/delegation/*`, and Codex bootstrap/generation contracts.

## Required Decision Fields
Every targeted/delegated routing preflight must include:
- `runtime_spawn_supported`
- `execution_mode_input`
- `classification`
- `required_roles`
- `available_adapters`
- `missing_adapters`
- `delegation_decision`
- `fallback_action`

`delegation_decision` must include:
- `main_runtime_allowed`
- `targeted_required`
- `delegated_allowed`
- `fallback_requires_approval`

## Regression Scenarios

| Scenario | Expected Classification/Decision | Required Roles | Fallback Rule |
|---|---|---|---|
| Frontend Tiny code change | `targeted_required: true`, `delegated_allowed: false` unless scope/risk escalates | `frontend` unless generated `vue`/`react` adapter is selected | Missing required adapter requires fallback disclosure and approval where targeted was required/requested |
| Backend Tiny code change | `targeted_required: true`, `delegated_allowed: false` unless scope/risk escalates | `backend` unless generated `fastapi`/`laravel`/`node-express`/`python` adapter is selected | Missing required adapter requires fallback disclosure and approval where targeted was required/requested |
| Test-only code change | `targeted_required: true`, `delegated_allowed: false` unless scope/risk escalates | `tester` | Missing `tester` adapter requires fallback disclosure and approval where targeted was required/requested |
| Pure review/no artifact | `main_runtime_allowed: true`, `targeted_required: false`, `delegated_allowed: false` | none; `reviewer` optional | No delegation claim; no audit artifact required |
| Review artifact | `main_runtime_allowed: false` when runtime subagents and adapters are available, `targeted_required: true` | `reviewer`, `documentation` | Missing adapter requires disclosure; approval required before sequential role simulation |
| Medium feature | Full delegated/planning-gated flow when capability and adapters pass | `project-manager` -> `product-spec` -> `architect` -> proposal approval -> implementation agents -> `tester` -> `reviewer` -> `documentation` | Implementation must stop before approval; missing required adapter requires disclosure and approval before simulation fallback |
| Missing required adapter | `fallback_requires_approval: true` for required/requested targeted/delegated routing | exact missing role(s) named in `missing_adapters` | Never silently collapse into parent/main; never claim delegation |
| Prompt-body `Execution mode: targeted` | Routing input only; not automatic spawn | derived from classification after preflight | Parent/main still classifies, checks capability/adapters, then explicitly invokes children only when gates pass |

## Planning Gate Regression
Medium/Large delegated implementation roles must wait for:
- `project-manager`
- `product-spec`
- `architect`
- required proposal artifacts as repository files
- consolidated proposal review package using `.ai/templates/proposal-review-package.md`
- explicit implementation approval

Tiny/Small targeted `backend`, `frontend`, and `tester` runs must not require `SPEC_APPROVED` or `ARCHITECTURE_READY` unless risk, scope, ambiguity, contract changes, architecture changes, or explicit user request escalates the task.

## Specialist Adapter Regression
Specialist roles (`security`, `devops`, `vue`, `react`, `fastapi`, `laravel`, `node-express`, `python`) are routable only when generated adapters exist or the runtime can invoke the canonical role explicitly. If missing:
- disclose the specialist gap,
- use generic `frontend`/`backend` only when classification allows it and the generic adapter is available,
- request approval before sequential role simulation where required,
- never claim delegation unless a child agent was actually spawned/invoked.
