# Claude Orchestration Bootstrap

## Purpose
Runtime-facing orchestration guidance for the Claude main session.

## Canonical Rule
- `AGENTS.md` and `.ai/*` remain canonical.
- This file is derivative runtime guidance only.
- If any conflict exists, canonical `.ai/*` wins.

## Pre-Preflight Exception Check
See `AGENTS.md § Pre-Preflight Exceptions`.

## Prompt Routing Contract
See `AGENTS.md § Prompt Routing Contract`.

## Classification Rule
See `INDEX.md § Classification Rule`.

## Worker Availability Rule
If generated Claude workers are present at `.claude/agents/*.md`:
- they are the default specialist workers for matching tasks,
- the Claude main session must delegate to them,
- adapters are derived from canonical sources at `.ai/agents/runtime/*.md`.

If a required Claude worker is absent:
- disclose the missing adapter by name,
- halt and await explicit user instruction (`no subagent` / `main only`),
- do not implement inline as a silent fallback,
- do not claim delegation occurred.

## Canonical Runtime Workers
See `AGENTS.md § Orchestration Baseline`.

## Persona Inheritance
See `AGENTS.md § Persona Inheritance`.

## Required Routing (5-Phase Flow)
See `INDEX.md § Recommended Flows`.

## Audit Rule
- Code-changing runs requiring artifacts (per `.ai/execution/artifact-conventions.md` and `.ai/policies/risk-classification.md`) must persist `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- If `documentation-writer` is skipped for Tiny work, the run report requirement is waived. If a run report is required, it must be written by a delegated agent (`documentation-writer` or `doc-team-lead`) — the main session must not write files under any circumstance.
- Run report agent participation must distinguish:
  - parent session orchestration work,
  - delegated child agents actually spawned/invoked.
- Do not report completion/participation for any child role that was never spawned.

## Planning Gate Enforcement
- Medium+ delegated flows require spawned Planning Council members in order, ending with an explicit pre-implementation confirmation when risk class or confidence-gated areas (auth, data model, public API, payments, deploy) apply.
- Parent/main may not replace `project-manager` with parent-authored planning in delegated Medium+ runs.
- Do not force the full Planning Council for Tiny/Small targeted delegation.

## Approval Gates
See `.ai/policies/approval-levels.md`.

## Safety
- Presence of `.claude/agents/*.md` does not authorize policy bypass.
- Use repository approval/safety policies for risky actions per `.ai/policies/approval-levels.md` and `.ai/policies/secrets-management.md`.
- Main session must remain orchestrator, dispatcher, integrator, and reviewer.
- Main session must not implement directly when a suitable Claude worker exists.
- Main session must not continue automatically from planning stages to implementation without passing the relevant Approval gate.
- Delegated specialists must provide scoped outputs/reports when subagent spawning is supported.
