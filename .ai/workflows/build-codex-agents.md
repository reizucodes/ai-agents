# Build Codex Agents Workflow

## Purpose
Define the Codex-specific workflow for generating and validating runtime adapter artifacts (`.codex/agents/*.toml`) from canonical runtime role contracts.

This workflow is a contract only. It does not generate files by itself.

## Scope
- Applies only when the user explicitly requests Codex adapter-generation work.
- Does not apply to normal feature, bugfix, refactor, review, or documentation tasks.

## Source-Repository Guard (Mandatory)
Before generating anything, check whether execution is happening in the ai-agents framework source repo.

**Primary detection:** `.ai/.framework-root` exists at repo root → this is the framework source repo.

If framework source is detected, stop and refuse generation by default. Do not create `.codex/agents/*` or `.ai/reports/codex-adapter-run-report.md`.

Required refusal response:
"Refusing to generate .codex/agents/* in the ai-agents source repository. Import the framework into a consumer project first, then run build-codex-agents there."

**Override (unsafe/diagnostic only):** Generation inside the framework source repo is allowed only when the user explicitly states `override: generate codex agents in source repo`. Without that exact override intent, generation remains blocked.

## Canonical and Adapter Clarifications
- `AGENTS.md` and `.ai/*` remain canonical.
- `.codex/agents/*.toml` are generated Codex adapters only.
- Canonical adapter sources are `.ai/agents/runtime/*.md` (exactly 16 files).
- `.ai/agents/personas/*.md` are NEVER adapter sources. They are inheritance-only skill docs loaded on demand by matching runtime agents.
- Generated adapters must not replace canonical role contracts.
- Codex subagents still require explicit runtime invocation.
- `.ai/execution/adapter-role-mapping.md` is the authoritative 16-role list.

## Required Loaded Contracts
Load all of the following before execution:
1. `AGENTS.md`
2. `INDEX.md`
3. `.ai/execution/runtime-adapter-contract.md`
4. `.ai/execution/adapter-role-mapping.md`
5. `.ai/execution/adapter-drift-validation.md`
6. `.ai/runtimes/codex/adapter-schema.md`
7. `.ai/runtimes/codex/nickname-strategy.md`
8. `.ai/policies/approval-levels.md`
9. `.ai/policies/quality-gates.md`
10. `.ai/policies/risk-classification.md`
11. The 16 canonical role source files from `.ai/agents/runtime/*.md`
12. Persona files at `.ai/agents/personas/*.md` (used only to describe inheritance, not as adapter sources)

## Inputs
- Explicit user request to build Codex adapters.
- Canonical runtime role contracts from `.ai/agents/runtime/*`.
- The 16-role list and persona-inheritance map in `.ai/execution/adapter-role-mapping.md`.
- Codex adapter schema (`.ai/runtimes/codex/adapter-schema.md`).
- Codex nickname strategy (`.ai/runtimes/codex/nickname-strategy.md`).
- Optional existing adapter files for drift validation.

## Outputs
- Exactly 16 adapter files at `.codex/agents/<role>.toml`, with filenames matching canonical runtime filenames (`.md` -> `.toml`).
- `.ai/runtimes/codex/orchestration-bootstrap.md` (refreshed when role set or routing rules change).
- `.ai/reports/codex-adapter-run-report.md`.

## Canonical Output Set (16)
The 16 adapter filenames are fixed by `.ai/execution/adapter-role-mapping.md`:
`backend-developer`, `cybersecurity-analyst`, `database-administrator`, `dev-team-lead`, `devops-engineer`, `doc-team-lead`, `documentation-writer`, `frontend-developer`, `junior-project-manager`, `pr-manager`, `project-manager`, `project-owner`, `qa-specialist`, `qa-team-lead`, `ui-ux-designer`, `web-designer`.

`project-manager` is the primary/orchestrator adapter. The other 15 are subagents.

Build workflow MUST refuse to generate any adapter whose name is not in this list. Build workflow MUST NOT generate adapters from `.ai/agents/personas/*`.

## Persona Inheritance Documentation
For the three inheriting roles, the generated adapter's `developer_instructions` must describe how the agent loads persona files on demand when task detection matches a stack:

- `backend-developer`: when working in a Laravel/PHP, FastAPI/Python, Node/Express, or generic Python backend, load `.ai/agents/personas/<stack>.md` for stack-specific patterns before performing the change.
- `frontend-developer`: when working in a React or Vue codebase, load `.ai/agents/personas/<stack>.md`.
- `web-designer`: when the surface renders through React or Vue, load `.ai/agents/personas/<stack>.md`.

Persona inheritance is a runtime directive. Persona files are NEVER copied into the adapter body and are NEVER emitted as `.codex/agents/*.toml`.

## Report Persistence Rule
- Every `build-codex-agents` run must write a persistent report artifact file.
- Default report output path: `.ai/reports/codex-adapter-run-report.md`.
- Final response summary does not replace the required report file artifact.

## Runtime Routing Rule
- Build workflow execution may run sequentially because adapter generation itself is a single builder task.
- Generated Codex workers must support delegation-first runtime behavior.
- The Codex main session is not an implementation agent and must delegate matching work whenever a suitable Codex worker exists.
- Runtime capability gates still apply per `.ai/policies/approval-levels.md`.

## Regeneration Invocation
- `Run .ai/workflows/build-codex-agents.md and generate the 16 adapters at .codex/agents/*.toml from canonical .ai/agents/runtime/* using .ai/execution/adapter-role-mapping.md and .ai/runtimes/codex/adapter-schema.md, then write .ai/reports/codex-adapter-run-report.md.`

## Source-to-Adapter Mapping Process
1. Load the canonical 16-role list from `.ai/execution/adapter-role-mapping.md`.
2. Resolve each canonical source path `.ai/agents/runtime/<name>.md`.
3. Build target Codex adapter plan per role:
   - source role file
   - adapter name (matches canonical filename)
   - adapter description
   - default write/forbidden scopes
   - delegation notes
   - phase participation
   - optional nickname candidates per `.ai/runtimes/codex/nickname-strategy.md`
   - display-name expectation: `<nickname> [<canonical-role>]`
4. Reject any plan entry whose name is not in the canonical 16-role set.
5. Build/update Codex runtime orchestration bootstrap from canonical contracts:
   - 5-phase flow routing (Planning Council, Implementation, QA, Business Go/No-Go, Commit/PR)
   - 3-gate approval model (Auto / Recommendation / Approval)
   - delegation-first routing
   - artifact requirements per `.ai/policies/risk-classification.md` and `.ai/execution/artifact-conventions.md`

## Codex TOML Generation Rules
When generation is requested:
1. Target output path pattern is `.codex/agents/<role>.toml`.
2. Required fields per adapter:
   - `name` (matches canonical filename)
   - `description`
   - `developer_instructions`
3. `developer_instructions` must use canonical-first pattern:

```toml
developer_instructions = """
This is a generated Codex adapter.

You are a delegated child agent, not the main session.

Before performing role work, read and follow the canonical role contract:
.ai/agents/runtime/<role>.md

This adapter is not the source of truth.
If this adapter conflicts with the canonical role contract, follow the canonical role contract.

You may execute the work your role and assigned scope require, subject to
.ai/policies/approval-levels.md and other applicable policies.

<short runtime-specific role summary, including delegation contract, ownership boundaries, deliverables,
and (for backend-developer, frontend-developer, web-designer) persona inheritance directive>
"""
```

Runtime-specific summary requirements (keep concise):
- Role identity: worker states who it is and what it owns.
- Child scope guard: worker states it is not the main session and may perform role-defined work within assigned scope and policy gates.
- Delegation triggers: worker states when parent/main must call it.
- Ownership boundaries: worker states what it may change and what it must not change.
- Deliverables: worker states required outputs and artifacts per `.ai/execution/artifact-conventions.md`.
- Delegation contract: parent/main must delegate matching work; worker owns delegated implementation; worker must not silently return ownership; worker must not collapse into orchestration behavior.
- Phase placement: state which of the 5 phases the worker participates in.
- Persona inheritance directive (only for `backend-developer`, `frontend-developer`, `web-designer`).
- Display rule: `<nickname> [<canonical-role>]`.

4. Include required generated metadata header (machine-readable comments) per `.ai/runtimes/codex/adapter-schema.md`:
   - generated-by, generated-at (UTC ISO-8601), canonical-source, canonical-source-fingerprint (`sha256:<hex>`), schema-ref.
5. Include canonical source pointer and drift metadata.
6. Keep adapter content minimal and scoped.
7. Do not duplicate full canonical role contracts.
8. Include `nickname_candidates` only when supported per `.ai/runtimes/codex/nickname-strategy.md`.
9. Treat `nickname_candidates` as advisory only; runtime may ignore.
10. Ensure generated `description`/`developer_instructions` keep canonical role visible and enforce display format `<nickname> [<canonical-role>]`.

## Validation Steps
Run validation using `.ai/execution/adapter-drift-validation.md` and `.ai/runtimes/codex/adapter-schema.md`:
1. Role-set integrity: exactly 16 outputs, names match `adapter-role-mapping.md`.
2. No outputs derived from `.ai/agents/personas/*`.
3. Canonical source existence check (`.ai/agents/runtime/<name>.md` exists).
4. Adapter output existence check.
5. Source fingerprint/checksum comparison.
6. Generated timestamp validation.
7. Required metadata validation.
8. Required field validation (`name`, `description`, `developer_instructions`).
9. Canonical source pointer validation.
10. Minimal-content / no-full-duplication validation.
11. Codex TOML schema validation.
12. Reserved-name/collision validation.
13. Nickname quality/collision validation when `nickname_candidates` are provided.
14. Display-name contract validation (`<nickname> [<canonical-role>]`).
15. Persona inheritance directive present for `backend-developer`, `frontend-developer`, `web-designer`.
16. Orchestration bootstrap presence/content validation at `.ai/runtimes/codex/orchestration-bootstrap.md`.

## Drift-Check Steps
1. Compute canonical source fingerprint for each of the 16 roles.
2. Read adapter fingerprint metadata.
3. Compare and classify state: in-sync / stale / missing metadata / missing adapter.
4. Produce remediation action per failure state.

## Rejection Conditions
Reject generation or validation when any apply:
- `.ai/.framework-root` present at repo root without explicit override phrase
- adapter name not in canonical 16-role set
- attempt to generate an adapter from `.ai/agents/personas/*`
- canonical source missing/ambiguous
- required runtime schema unknown/unavailable
- required metadata cannot be produced
- adapter name collision without explicit user-approved override
- request attempts to overwrite canonical source
- unsafe duplication of full canonical contracts
- policy/quality gate bypass attempts
- required Codex orchestration bootstrap cannot be produced from canonical contracts

## Safety Rules
- Do not treat generated adapters as canonical source.
- Do not claim delegated execution unless delegation actually occurs.
- Do not claim Codex subagent execution unless explicitly invoked by runtime.
- Generated Codex workers are the default specialist workers when present.
- Do not depend on runtime display nicknames for routing, validation, reporting, or handoffs.
- Require display/self-identification format `<nickname> [<canonical-role>]`.
- Preserve host-project approval and safety policy requirements per `.ai/policies/approval-levels.md`.

## Reporting Format
Each run must use `.ai/templates/adapter-run-report.md` and report:
1. Loaded contracts.
2. Adapter targets considered (canonical adapter `name` + canonical role path).
3. Mapping decisions (the 16-role set; persona files excluded).
4. Validation/drift result per target: `pass` or `fail`, failure state(s), remediation action(s).
5. Generated outputs (if any) and unresolved risks.
6. Report artifact path written to disk.
7. Orchestration bootstrap artifact path written to disk.
