# Delegation Eligibility Contract

## Purpose
Decide whether delegated execution is appropriate for a specific task.

## Follow-Up Reclassification Rule
Every follow-up task must be reclassified before execution.

- Follow-up tasks inherit current feature context.
- Follow-up tasks do not inherit previous delegation decisions.
- Delegation must be decided from current follow-up classification and risk.

## Eligibility Inputs
- Task classification from `.ai/execution/task-classification.md`.
- Follow-up type (Tiny, Small single-surface, Small multi-surface, Medium/Large).
- Task size and coupling.
- Risk level and governance requirements.
- Runtime capability gate result.
- Availability of disjoint ownership boundaries.

## Follow-Up Delegation Thresholds

### Tiny follow-up
Examples:
- explain how to run
- fix typo
- minor config adjustment
- one-line bug fix

Default:
- parent may handle directly.

### Small single-surface follow-up
Examples:
- backend-only CORS fix
- frontend-only copy/UI tweak
- docs-only summary

Default:
- parent may handle directly.
- delegation optional.

### Small multi-surface follow-up
Examples:
- backend endpoint + frontend view
- API change + tests
- UI change + docs

Default:
- parent must consider targeted subagents.
- if parent does not delegate, parent must state why.

### Medium/Large follow-up
Default:
- use delegated flow or planning-gate flow.
- require finalized spec or active Requirement Clarification Gate before implementation delegation.
- require architecture handoff before spawning implementation executors.

## Eligible Cases
Delegated mode is preferred when all apply:
- Task can be decomposed into independent subproblems.
- Write scopes can be separated clearly.
- Parallel execution reduces critical-path time materially.
- Parent can still enforce policy gates and final validation.
- Required planning outputs exist when classification is Medium/Large.
- For Medium/Large:
  - approved consolidated spec exists, or spec gate is actively unresolved and implementation is paused.
  - architecture handoff exists before spawning `backend`/`frontend`.

## Ineligible Cases (Reject Delegation)
Reject delegated mode when any apply:
- Tiny/single-file/simple change.
- Small single-surface change with no measurable parallelism benefit.
- Heavily coupled changes likely to conflict.
- Contract/migration/auth changes requiring tightly sequenced edits.
- Runtime subagent support is unknown/unreliable.
- Medium/Large task without completed planning outputs.
- Medium/Large task where spec is not approved and no active spec gate is present.
- Medium/Large task without architecture handoff before executor spawn.
- Auditability requires single-thread deterministic trace.

## Skip-Delegation Explanation Rule
When parent handles a small multi-surface follow-up directly, parent must include:
- `Classification: Small multi-surface`
- `Delegation decision: skipped`
- `Reason: <low-risk | tightly coupled | faster parent patch | user did not request full delegation>`

## Decision Output
- `sequential_required`
- `delegated_allowed`

## Enforcement
- Default output is `sequential_required` unless delegated conditions are explicitly met.
- If delegated mode starts and preconditions fail mid-run, parent must fall back to sequential completion.
