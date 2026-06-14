# Delegation Eligibility Contract

## Purpose
Decide which delegated specialist path is required for a specific task and when approved fallback is the only remaining option.

## Follow-Up Reclassification Rule
Every follow-up task must be reclassified before execution.

- Follow-up tasks inherit current feature context.
- Follow-up tasks do not inherit previous delegation decisions.
- Delegation must be decided from current follow-up classification and risk.

## Eligibility Inputs
- Task classification from `.ai/execution/task-classification.md`.
- Execution-mode input from runtime metadata or prompt-body fallback.
- Follow-up type (Tiny, Small single-surface, Small multi-surface, Medium/Large).
- Task size and coupling.
- Risk level and governance requirements.
- Runtime capability gate result.
- Role-specific adapter preflight result from `.ai/execution/capability-gates.md`.
- Availability of disjoint ownership boundaries.
- Whether the run is code-changing.

Definition:
- Targeted delegation: spawn only the minimal relevant implementation role(s) needed for the changed surface, without forcing full Medium/Large planning pipeline.
- Review artifact-generating tasks: review/report/audit/final-report producing tasks that must route to `reviewer` and `documentation` (when run-report/audit/final-report artifacts are produced).

## Follow-Up Delegation Thresholds

### Tiny follow-up
Examples:
- explain how to run
- fix typo
- minor config adjustment
- one-line bug fix

Default:
- parent may handle directly only when non-code-changing.
- code-changing Tiny work produces `targeted_required`.
- full `delegated` mode should be rejected unless risk/scope escalates beyond Tiny.

### Small single-surface follow-up
Examples:
- backend-only CORS fix
- frontend-only copy/UI tweak
- documentation-only summary

Default:
- non-code-changing work may be parent-direct.
- code-changing work produces `targeted_required`.
- full `delegated` mode should be rejected unless planning or parallelism is justified by escalated risk/scope.

### Small multi-surface follow-up
Examples:
- backend endpoint + frontend view
- API change + tests
- UI change + documentation

Default:
- code-changing work produces `targeted_required`.
- full `delegated` mode is allowed only when the work can be decomposed into disjoint ownership boundaries with meaningful coordination benefit.

### Medium/Large follow-up
Default:
- use full delegated flow or planning-gate flow when the required worker path is available.
- require finalized spec or active Requirement Clarification Gate before implementation delegation.
- require architecture handoff before spawning implementation executors.

## Eligible Cases
Full delegated mode is preferred when all apply:
- Task can be decomposed into independent subproblems.
- Write scopes can be separated clearly.
- Parallel execution reduces critical-path time materially.
- Parent can still enforce policy gates and final validation.
- Required planning outputs exist when classification is Medium/Large.
- For Medium/Large:
  - approved consolidated spec exists, or spec gate is actively unresolved and implementation is paused.
  - architecture handoff exists before spawning `backend`/`frontend`.
- For review artifact-generating tasks:
  - delegated mode should be selected automatically when runtime capability gates pass.
  - `reviewer` is required.
  - `documentation` is required when run report/audit/final report artifact is created.
  - `tester` is required when validation/coverage/test-result verification is in scope.

## Ineligible Cases (Reject Delegation)
Reject full delegated mode when any apply:
- Tiny/single-file/simple change.
- Small single-surface change with no measurable parallelism benefit.
- Heavily coupled changes likely to conflict.
- Contract/migration/auth changes requiring tightly sequenced edits.
- Runtime subagent support is unknown/unreliable.
- Medium/Large task without completed planning outputs.
- Medium/Large task where spec is not approved and no active spec gate is present.
- Medium/Large task without architecture handoff before executor spawn.
- Auditability requires single-thread deterministic trace.
- A fallback audit report cannot be produced for a code-changing run.

Review-only sequential exception:
- Parent-only sequential execution is allowed for pure review/analysis only when no artifacts are created/updated.
- Parent-only sequential execution is not allowed for review artifact-generating tasks when runtime delegation is available.

## Fallback Approval Rule
When parent cannot delegate matching implementation work because required worker capability is unavailable, parent must:
- disclose the missing capability/worker explicitly,
- request explicit user approval before sequential role simulation fallback,
- avoid claiming delegation occurred,
- honor explicit user bypasses `no subagent` and `main only`.

## Decision Output
Every routing decision must report these fields:

- `main_runtime_allowed`: `true` when parent/main may complete the task without spawning child agents.
- `targeted_required`: `true` when the classified task requires minimal relevant child roles.
- `delegated_allowed`: `true` when full delegated workflow is appropriate and planning/parallelism gates pass.
- `fallback_requires_approval`: `true` when the requested/required `targeted` or `delegated` path cannot run because runtime subagents or required adapters are unavailable, and the only viable alternative is sequential role simulation.

Decision rules:
- Pure review/analysis with no artifact output:
  - `main_runtime_allowed: true`
  - `targeted_required: false`
  - `delegated_allowed: false`
  - `fallback_requires_approval: false`
- Review artifact-generating task:
  - `main_runtime_allowed: false` when the required worker path is available
  - `targeted_required: true`
  - required roles: `reviewer`, `documentation`
- Tiny/Small code-changing task:
  - `main_runtime_allowed: false` when the required worker path is available
  - `targeted_required: true`
  - `delegated_allowed: false` unless risk/scope escalates beyond Tiny/Small or parallel ownership materially helps
- Medium/Large task:
  - `main_runtime_allowed: false` for implementation after planning gates apply
  - `targeted_required: false` before planning completion unless a scoped non-planning follow-up is classified Tiny/Small
  - `delegated_allowed: true` only after planning/proposal approval gates and required adapter preflight pass
- Missing required adapter or unsupported subagent capability:
  - `fallback_requires_approval: true` for requested/required `targeted` or `delegated`
  - fallback disclosure must name missing capability/adapters and must not claim delegation.

## Enforcement
- Default output is `main_runtime_allowed: true` only for non-code-changing or pure review/analysis work where no artifact output is required, or when the user explicitly says `no subagent` or `main only`.
- If `targeted_required` or `delegated_allowed` is true, parent/main must run role-specific preflight before spawning.
- If targeted/delegated preconditions fail before execution, parent/main must disclose fallback and request approval where `fallback_requires_approval` is true.
- If targeted/delegated mode starts and preconditions fail mid-run, parent must stop, disclose the failure, and request approval before sequential role simulation fallback when the contracts require it.
- Sequential role simulation still must persist `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md` for code-changing runs and must identify simulated roles as simulated, not delegated.
