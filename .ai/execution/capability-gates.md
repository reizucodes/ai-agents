# Execution Capability Gates

## Purpose
Define hard capability checks required before using delegated execution.

## Gate Categories

### Gate 0: Role-Specific Runtime Adapter Discovery (Codex)
For Codex runtime task routing that expects generated adapters:
- Determine the exact `required_roles` from task classification, scope, risk, and execution-mode input.
- Check whether a matching `.codex/agents/<role>.toml` exists for each required role.
- Do not treat the existence of any `.codex/agents/*.toml` as sufficient.
- Record `available_adapters` and `missing_adapters` by canonical role name.

If any required adapter is missing for a run that requests/needs `targeted` or `delegated` adapter-based routing:
- Disclose the limitation explicitly.
- Request user approval before sequential role simulation fallback.
- Do not claim delegation occurred.
- If a specialist adapter is missing but a generic owning adapter is available, use the generic owning adapter only when the classification selected it as an approved fallback and disclose the specialist gap.

### Gate 1: Runtime Capability
All must be true:
- Runtime can spawn subagents/children explicitly.
- Runtime can return child outputs to parent.
- Runtime can enforce or preserve child context isolation.
- Runtime can run with bounded concurrency controls.

If any is unknown:
- Treat as not supported.
- Disclose the limitation explicitly.
- Request user approval before sequential role simulation fallback.
- Do not claim delegation occurred.

### Gate 2: Invocation Integrity
All must be true:
- Parent chooses execution mode from classification + eligibility (no user "delegated mode" request required).
- Parent explicitly requests/spawns child agents.
- Parent does not claim delegation if no child agents were spawned.

If false:
- Disclose the limitation explicitly.
- Request user approval before sequential role simulation fallback.
- Do not claim delegation occurred.

### Gate 3: Governance Compatibility
All must be true:
- Required approvals remain enforceable.
- Quality and definition-of-done gates remain enforceable.
- Security/risk policy application remains intact.

If false:
- Disclose the limitation explicitly.
- Request user approval before sequential role simulation fallback.
- Do not claim delegation occurred.

### Gate 4: Merge Control
All must be true:
- Parent can assign disjoint file ownership.
- Parent can integrate child outputs deterministically.
- Parent can resolve conflicts and preserve review traceability.

If false:
- Disclose the limitation explicitly.
- Request user approval before sequential role simulation fallback.
- Do not claim delegation occurred.

### Gate 5: Planning Gate Compliance
For Medium/Large classification, all must be true before implementation delegation:
- Planning phase output exists (`project-manager`).
- Product specification output exists (`product-spec` or PRD equivalent).
- Technical design output exists (`architect` or technical design equivalent).
- Required proposal artifacts exist as repository files.
- Parent/orchestrator has presented consolidated proposal package to user.
- Explicit user approval for implementation is recorded.

If false:
- Stop and resolve planning/artifact/approval gaps first.
- Do not start implementation roles (`backend`, `frontend`, or other code-writing roles).

### Required Preflight Output
Before `targeted` or `delegated` execution, parent/main must emit or internally record a preflight result with:

- `runtime_spawn_supported`: `true|false|unknown`
- `execution_mode_input`: `auto|sequential|targeted|delegated` plus source (`runtime_metadata|prompt_body_fallback|default`)
- `classification`: task size/type and code-changing/artifact status
- `required_roles`: exact canonical child role names required for this task
- `available_adapters`: required role adapters found and usable
- `missing_adapters`: required role adapters not found or stale/unusable
- `delegation_decision`: object containing `main_runtime_allowed`, `targeted_required`, `delegated_allowed`, `fallback_requires_approval`
- `fallback_action`: `none|parent_main|sequential_role_simulation_pending_approval|sequential_role_simulation_approved|stop_for_missing_capability`

Role selection examples:
- frontend Tiny code change: `required_roles: [frontend]` unless a generated `vue` or `react` adapter is explicitly required by classification.
- backend Tiny code change: `required_roles: [backend]` unless a generated `fastapi`, `laravel`, or `node-express` adapter is explicitly required by classification.
- test-only code change: `required_roles: [tester]`.
- pure review/no artifact: `required_roles: []`, reviewer optional.
- review artifact: `required_roles: [reviewer, docs]`.
- Medium feature implementation after approval: `required_roles` includes planning roles for the current phase and implementation roles for the approved phase (`project-manager`, `product-spec`, `architect`, then implementation agents, `tester`, `reviewer`, `docs`).

## Capability Decision
`targeted` and `delegated` modes are allowed only when all required gates and role-specific adapter checks pass.
When an explicit execution-mode input is provided, enforce `.ai/execution/execution-mode-input.md`.

## Non-claims
- Presence of `.ai/agents/*` does not imply runtime delegation capability.
- Presence of delegation contracts does not imply runtime delegation capability.
- Runtime support must be confirmed at execution time.
