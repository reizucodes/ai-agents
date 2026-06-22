# Delegation Routing Regression Coverage

## Purpose
Capture routing regressions that must remain unambiguous across `INDEX.md`, `.ai/execution/*`, `.ai/delegation/*`, and runtime bootstrap/generation contracts.

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

See `.ai/execution/artifact-conventions.md` for the full preflight output template.

## Regression Scenarios

| Scenario | Expected Classification/Decision | Required Roles | Fallback Rule |
|---|---|---|---|
| Framework-native context (`.ai/.framework-root` at root) | Exception A applies before preflight; `main_runtime_allowed: true`, delegation suspended | none | Main session acts directly; native subagents allowed; 16-role routing does not apply |
| Build-bootstrap operation (`build claude/codex/opencode agents`) | Exception B applies before preflight; `main_runtime_allowed: true`, delegation suspended | none | Main session executes build workflow directly; adapter presence not checked |
| Frontend Tiny code change | `targeted_required: true`, `delegated_allowed: false` unless scope/risk escalates | `frontend-developer` (with `personas/vue` or `personas/react` inherited when stack matches) | Missing required adapter requires fallback disclosure and approval where targeted was required/requested |
| Backend Tiny code change | `targeted_required: true`, `delegated_allowed: false` unless scope/risk escalates | `backend-developer` (with `personas/fastapi`, `personas/laravel`, `personas/node-express`, or `personas/python` inherited when stack matches) | Missing required adapter requires fallback disclosure and approval where targeted was required/requested |
| Test-only code change | `targeted_required: true`, `delegated_allowed: false` unless scope/risk escalates | `qa-specialist` | Missing `qa-specialist` adapter requires fallback disclosure and approval |
| Pure review/no artifact | `main_runtime_allowed: true`, `targeted_required: false`, `delegated_allowed: false` | none; `pr-manager` optional | No delegation claim; no audit artifact required |
| Review artifact | `main_runtime_allowed: false` when runtime subagents/adapters available, `targeted_required: true` | `pr-manager`, `documentation-writer` | Missing adapter requires disclosure; approval required before simulation |
| Schema change | `targeted_required: true` (or planning-gated when Medium+) | `database-administrator` + `dev-team-lead`; requires `technical-design.md` + migration/rollback notes | Missing `database-administrator` adapter requires disclosure |
| Security-sensitive change | Planning-gated when Medium+ | `cybersecurity-analyst` + relevant implementer; requires `threat-model.md` + proposal review package | Missing `cybersecurity-analyst` adapter requires disclosure |
| Medium feature | Planning Council → implementation → QA → business go/no-go → PR | `project-owner`, `project-manager`, `dev-team-lead` → implementation agents → `qa-specialist` + `qa-team-lead` → `pr-manager` + `documentation-writer` | Implementation must stop before approval; missing required adapter requires disclosure |
| Major feature / refactor | Full 5-phase flow with proposal approval gate | Planning Council + `cybersecurity-analyst` (if sensitive) + `ui-ux-designer` (if UI) → implementation → QA → business gate → PR | Implementation stops until proposal approval; missing adapter requires disclosure |
| Deployment-impacting | Release workflow | `devops-engineer` + `pr-manager` + `documentation-writer`; requires release workflow + run-report | Missing `devops-engineer` adapter requires disclosure |
| Missing required adapter | `fallback_requires_approval: true` for required/requested targeted/delegated routing | exact missing role(s) named in `missing_adapters` | Never silently collapse into parent/main; never claim delegation |
| Prompt-body `Execution mode: targeted` | Routing input only; not automatic spawn | derived from classification after preflight | Parent/main still classifies, checks capability/adapters, then explicitly invokes children only when gates pass |

## Planning Gate Regression
Medium/Major delegated implementation roles must wait for:
- Planning Council outputs (`project-owner`, `project-manager`, `dev-team-lead`),
- required proposal artifacts as repository files (per `.ai/execution/artifact-conventions.md`),
- consolidated proposal review package using `.ai/templates/proposal-review-package.md`,
- explicit implementation approval.

Tiny/Small targeted runs do not require `SPEC_APPROVED` or `ARCHITECTURE_READY` unless risk, scope, ambiguity, contract changes, architecture changes, or explicit user request escalates the task.

## Persona Inheritance Regression
Stack personas (`vue`, `react`, `laravel`, `fastapi`, `node-express`, `python`) are **not** spawnable child roles. They live in `.ai/agents/personas/` and are injected into the prompt of the matching runtime worker (`frontend-developer` or `backend-developer`) when task detection identifies the stack. If a runtime worker adapter is missing:
- disclose the missing canonical worker,
- request approval before sequential role simulation,
- never claim delegation unless a child was actually spawned/invoked.
