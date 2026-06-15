# Build Codex Agents Workflow

## Purpose
Define a Codex-specific workflow for generating and validating derivative adapter artifacts (future `.codex/agents/*.toml`) from canonical role contracts.

This workflow is a contract only. It does not generate files by itself.

## Scope
- Applies only when the user explicitly requests Codex adapter-generation work.
- Does not apply to normal feature, bugfix, refactor, review, or documentation tasks.

## Canonical and Adapter Clarifications
- `.ai/agents/*` remains canonical.
- `.codex/agents/*.toml` are generated Codex adapters only.
- Generated adapters must not replace canonical role contracts.
- Codex subagents still require explicit runtime invocation.
- `.ai/execution/adapter-role-mapping.md` is generation-time mapping.
- `.ai/delegation/child-role-contracts.md` is delegated execution-time mapping.

## Required Loaded Contracts
Load all of the following before execution:
1. `.ai/execution/execution-mode-input.md`
2. `.ai/execution/runtime-adapter-contract.md`
3. `.ai/execution/adapter-role-mapping.md`
4. `.ai/execution/adapter-drift-validation.md`
5. `.ai/runtimes/codex/adapter-schema.md`
6. `.ai/runtimes/codex/nickname-strategy.md`
7. Canonical role source files from `.ai/agents/*` referenced by role mapping

## Inputs
- Explicit user request to build Codex adapters.
- Canonical role contracts from `.ai/agents/*`.
- Role mapping contract.
- Codex adapter schema contract.
- Codex nickname strategy contract (optional nickname metadata behavior).
- Optional existing adapter files for drift validation.

## Outputs
- Generation plan for target adapters.
- Validation report per adapter candidate:
  - pass/fail status,
  - drift state,
  - schema validation state,
  - rejection/remediation actions.
- Optional generated adapter artifacts (only when generation is explicitly requested and allowed).
- Persistent adapter run report file formatted with `.ai/templates/adapter-run-report.md`.
- Runtime-facing orchestration bootstrap artifact for Codex main session:
  - `.ai/runtimes/codex/orchestration-bootstrap.md`
  - generated/maintained from canonical `.ai/*` contracts.

## Report Persistence Rule
- Every `build-codex-agents` run must write a persistent report artifact file.
- Default report output path: `.ai/reports/codex-adapter-run-report.md`.
- The final response may summarize the run, but does not replace the required report file artifact.
- If a user explicitly provides a different report path, use that path; otherwise use the default path.

## Runtime Routing Rule
- Build workflow execution may run sequentially because adapter generation itself is a single builder task.
- The generated Codex workers must support delegation-first runtime behavior.
- The Codex main session is not an implementation agent and must delegate matching work whenever a suitable Codex worker exists.
- Runtime capability gates still apply, but execution-mode metadata does not decide whether delegation is required.

## Regeneration Invocation
Use this runtime command/prompt for regeneration:
- `Run .ai/workflows/build-codex-agents.md and generate .codex/agents/*.toml from canonical .ai/agents/* using .ai/runtimes/codex/adapter-schema.md, then write .ai/reports/codex-adapter-run-report.md.`

## Source-to-Adapter Mapping Process
1. Load generation mapping from `.ai/execution/adapter-role-mapping.md`.
2. Resolve each mapped canonical source path.
3. Build target Codex adapter plan using canonical adapter `name` as authoritative identifier:
   - source role file,
   - adapter name,
   - adapter description,
   - default write/forbidden scopes,
   - delegation notes,
   - optional nickname candidates when allowed.
   - display-name format expectation: `<nickname> [<canonical-role>]`.
4. Exclude opt-in roles unless explicitly requested by user/policy.
5. Build/update Codex runtime orchestration bootstrap artifact from canonical contracts:
   - execution-mode input model as secondary routing metadata (`auto|sequential|targeted|delegated`),
   - task classification routing,
   - delegation-first routing,
   - targeted delegation rules,
   - Medium/Large delegated flow gates,
   - docs/audit run-report requirements.

## Codex TOML Generation Rules
When generation is requested:
1. Target output path pattern is `.codex/agents/*.toml`.
2. Required fields per adapter:
   - `name`
   - `description`
   - `developer_instructions`
3. `developer_instructions` must use canonical-first pattern:

```toml
developer_instructions = """
This is a generated Codex adapter.

Before performing role work, read and follow the canonical role contract:
.ai/agents/<role>.md

This adapter is not the source of truth.
If this adapter conflicts with the canonical role contract, follow the canonical role contract.

<short runtime-specific role summary here>
"""
```

Runtime-specific summary requirements (keep concise):
- Discovery/spec-first role order:
  - `project-manager` -> `product-spec` -> approved consolidated spec -> `architect` -> `backend`/`frontend` -> `tester` -> `reviewer` -> `documentation`.
- Role identity:
  - worker states who it is and what it owns.
- Delegation triggers:
  - worker states when parent/main must call it.
- Ownership boundaries:
  - worker states what it may change and what it must not change.
- Deliverables:
  - worker states required outputs, report format, and artifacts.
- Delegation contract:
  - parent/main must delegate matching work,
  - worker owns delegated implementation,
  - worker must not silently return implementation ownership to parent/main,
  - worker must not collapse into orchestration behavior.
- Medium/Large delegated `backend`/`frontend` must not execute before approved consolidated spec and architect handoff.
- Tiny/Small targeted `backend`/`frontend`/`tester` runs do not require approved consolidated spec or architect handoff unless risk/scope escalates.
- `tester` validates against approved consolidated spec and acceptance criteria.
- `reviewer` runs after tester outputs.
- `documentation` runs last before parent final validation.
- when `documentation` runs, it writes `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- for Tiny/Small code-changing runs where `documentation` is skipped for efficiency, parent/main writes `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- code-changing runs require relevant implementation role usage:
  - frontend-only: `frontend`; generated framework specialist (`vue`/`react`) only when selected by classification,
  - backend-only: `backend`; generated framework specialist (`fastapi`/`laravel`/`node-express`/`python`) only when selected by classification,
  - cross-layer: both `backend` + `frontend`,
  - documentation-only: `documentation`,
  - test-only: `tester`.
- specialist adapter gaps must be disclosed; generic `frontend`/`backend` fallback is allowed only when available and sufficient for the classified scope.
- Implementation requirement ambiguity escalates through parent/`product-spec`, not direct user questioning by implementation adapters.
- Adapter self-identification/display rule:
  - Use `<nickname> [<canonical-role>]` in traces/logs when nickname is available.
  - If runtime-selected nickname differs, self-identify as `<runtime-nickname-or-configured-nickname> [<canonical-role>]`.

Runtime orchestration bootstrap minimum routing rules:
- execution-mode input header support:
  - `Execution mode: auto|sequential|targeted|delegated`.
- execution mode should be consumed from runtime/launcher metadata first.
- prompt-body `Execution mode: ...` is fallback only.
- prompt-body `Execution mode: ...` is routing input only and does not automatically spawn agents or decide whether delegation is required.
- when no mode is provided, default to `auto`.
- before specialist routing, parent/main must classify the task, check runtime spawn support, check exact required role adapters, and explicitly invoke children only after gates pass.
- required preflight output fields:
  - `runtime_spawn_supported`
  - `execution_mode_input`
  - `classification`
  - `required_roles`
  - `available_adapters`
  - `missing_adapters`
  - `delegation_decision`
  - `fallback_action`
- full-project review with artifact output -> `reviewer` -> `documentation`.
- review + validation -> `reviewer` -> `tester` (as needed) -> `documentation`.
- Tiny/Small frontend code change -> `targeted_required`, required role `frontend` unless generated `vue`/`react` adapter is selected -> audit report.
- Tiny/Small backend code change -> `targeted_required`, required role `backend` unless generated `fastapi`/`laravel`/`node-express`/`python` adapter is selected -> audit report.
- test-only code change -> `targeted_required`, required role `tester` -> audit report.
- Medium/Large feature -> `project-manager` -> `product-spec` -> `architect` -> proposal artifact validation + consolidated proposal package + explicit approval -> `backend`/`frontend` -> `tester` -> `reviewer` -> `documentation`.
- remediation/final-rerun flows -> targeted agents, then `tester` -> `reviewer` -> `documentation` when in scope.
- sequential fallback is allowed only when subagent capability or required adapter gates fail, and fallback must be disclosed.
- for `targeted`/`delegated`, sequential role simulation requires approval where `fallback_requires_approval` is true.
- never claim delegation unless a child agent was actually spawned/invoked.

4. Include required generated metadata header from runtime/schema contracts.
5. Include canonical source pointer and drift metadata.
6. Keep adapter content minimal and scoped.
7. Do not duplicate full canonical role contracts unless runtime requirement is explicitly documented.
8. Include `nickname_candidates` only when supported and requested according to `.ai/runtimes/codex/nickname-strategy.md`.
9. Treat `nickname_candidates` as advisory only; runtime may ignore them.
10. Ensure generated `description`/`developer_instructions` keep canonical role visible and enforce display format `<nickname> [<canonical-role>]`.

## Validation Steps
Run validation using `.ai/execution/adapter-drift-validation.md` and `.ai/runtimes/codex/adapter-schema.md`:
1. Canonical source existence check.
2. Adapter output existence check (for validation runs).
3. Source fingerprint/checksum comparison.
4. Generated timestamp validation.
5. Required metadata validation.
6. Required field validation.
7. Canonical source pointer validation.
8. Minimal-content/no-full-duplication validation.
9. Codex schema validation.
10. Reserved-name/collision validation when known names are available.
11. Nickname quality/collision validation when `nickname_candidates` are provided.
12. Display-name contract validation:
   - generated guidance requires `<nickname> [<canonical-role>]`,
   - canonical role suffix is always present,
   - self-identification fallback rule exists when runtime nickname differs.
13. Orchestration bootstrap presence/content validation:
   - file exists at `.ai/runtimes/codex/orchestration-bootstrap.md`,
   - routes are derived from canonical `.ai/*` contracts,
   - required routing rules above are present,
   - prompt-body execution mode is described as routing input only,
   - role-specific adapter preflight fields are present.

## Drift-Check Steps
1. Compute canonical source fingerprint/checksum for each mapped role.
2. Read adapter fingerprint/checksum metadata.
3. Compare and classify state:
   - in-sync,
   - stale,
   - missing metadata,
   - missing adapter.
4. Produce remediation action per failure state.

## Rejection Conditions
Reject generation or validation when any apply:
- canonical source missing/ambiguous,
- required runtime schema unknown/unavailable,
- required metadata cannot be produced,
- adapter name collision without explicit user-approved override,
- request attempts to overwrite canonical source,
- unsafe duplication of full canonical contracts,
- policy/quality gate bypass attempts.
- required Codex orchestration bootstrap cannot be produced from canonical contracts.

## Safety Rules
- Do not treat generated adapters as canonical source.
- Do not claim delegated execution unless delegation actually occurs.
- Do not claim Codex subagent execution unless explicitly invoked by runtime.
- Generated Codex workers are the default specialist workers when present.
- Do not depend on runtime display nicknames for routing, validation, reporting, or handoffs.
- Require runtime-facing display/self-identification guidance to use `<nickname> [<canonical-role>]`.
- Preserve host-project approval and safety policy requirements.
- Fall back to sequential mode only with required disclosure and approval where targeted/delegated preconditions fail.

## Reporting Format
Each run must use `.ai/templates/adapter-run-report.md` and report:
1. Mode used:
   - `sequential`
   - `delegated` (only when allowed and selected by parent/orchestrator)
2. Loaded contracts.
3. Adapter targets considered (canonical adapter `name` + canonical role path).
4. Mapping decisions and excluded roles.
5. Validation/drift result per target:
   - `pass` or `fail`,
   - failure state(s),
   - remediation action(s).
6. Generated outputs (if any) and unresolved risks.
7. Report artifact path written to disk.
8. Orchestration bootstrap artifact path written to disk.
