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
1. `.ai/execution/runtime-adapter-contract.md`
2. `.ai/execution/adapter-role-mapping.md`
3. `.ai/execution/adapter-drift-validation.md`
4. `.ai/runtimes/codex/adapter-schema.md`
5. `.ai/runtimes/codex/nickname-strategy.md`
6. Canonical role source files from `.ai/agents/*` referenced by role mapping

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

## Report Persistence Rule
- Every `build-codex-agents` run must write a persistent report artifact file.
- Default report output path: `.ai/reports/codex-adapter-run-report.md`.
- The final response may summarize the run, but does not replace the required report file artifact.
- If a user explicitly provides a different report path, use that path; otherwise use the default path.

## Execution Mode Rule
- Sequential-first execution is required.
- Delegated mode is not default.
- Delegation is optional only if all are true:
  1. consuming runtime supports subagents,
  2. capability gates pass,
  3. delegation eligibility passes,
  4. user explicitly requests delegated execution,
  5. generation work can be decomposed into independent workstreams.
- For normal adapter generation, execute sequentially.

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
4. Exclude opt-in roles unless explicitly requested by user/policy.

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
  - `project-manager` -> `product-spec` -> approved consolidated spec -> `architect` -> `backend`/`frontend` -> `tester` -> `reviewer` -> `docs`.
- `backend`/`frontend` must not execute before approved consolidated spec and architect handoff.
- `tester` validates against approved consolidated spec and acceptance criteria.
- `reviewer` runs after tester outputs.
- `docs` runs last before parent final validation.
- when `docs` runs, it writes `/artifacts/docs/<run-id>-run-report.md`.
- for Tiny/Small code-changing runs where `docs` is skipped for efficiency, parent/main writes `/artifacts/docs/<run-id>-run-report.md`.
- code-changing runs require relevant implementation role usage:
  - frontend-only: `frontend`/framework specialist,
  - backend-only: `backend`/framework specialist,
  - cross-layer: both `backend` + `frontend`,
  - docs-only: `docs`,
  - test-only: `tester`.
- Implementation requirement ambiguity escalates through parent/`product-spec`, not direct user questioning by implementation adapters.

4. Include required generated metadata header from runtime/schema contracts.
5. Include canonical source pointer and drift metadata.
6. Keep adapter content minimal and scoped.
7. Do not duplicate full canonical role contracts unless runtime requirement is explicitly documented.
8. Include `nickname_candidates` only when supported and requested according to `.ai/runtimes/codex/nickname-strategy.md`.
9. Treat `nickname_candidates` as advisory only; runtime may ignore them.

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

## Safety Rules
- Do not treat generated adapters as canonical source.
- Do not claim delegated execution unless delegation actually occurs.
- Do not claim Codex subagent execution unless explicitly invoked by runtime.
- Do not depend on runtime display nicknames for routing, validation, reporting, or handoffs.
- Preserve host-project approval and safety policy requirements.
- Fall back to sequential mode if delegated preconditions fail.

## Reporting Format
Each run must use `.ai/templates/adapter-run-report.md` and report:
1. Mode used:
   - `sequential`
   - `delegated` (only when allowed and explicitly requested)
2. Loaded contracts.
3. Adapter targets considered (canonical adapter `name` + canonical role path).
4. Mapping decisions and excluded roles.
5. Validation/drift result per target:
   - `pass` or `fail`,
   - failure state(s),
   - remediation action(s).
6. Generated outputs (if any) and unresolved risks.
7. Report artifact path written to disk.
