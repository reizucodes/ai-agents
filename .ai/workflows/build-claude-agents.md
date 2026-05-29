# Build Claude Agents Workflow

## Purpose
Define a Claude-specific workflow for generating and validating derivative project-level subagent adapters from canonical role contracts.

This workflow is a contract only. It does not generate files by itself.
This workflow defines how to generate `.claude/agents/*.md`.
Adapters are generated only when this workflow is explicitly invoked.
Repository installation, cloning, or first Claude Code execution must not generate adapters automatically.

## Scope
- Applies only when the user explicitly requests Claude adapter generation/validation work.
- Does not apply to normal feature, bugfix, refactor, review, or documentation tasks.

## Canonical and Adapter Clarifications
- `AGENTS.md` and `.ai/*` remain canonical.
- `.claude/agents/*.md` are generated Claude runtime adapters only.
- Generated adapters are build artifacts.
- Canonical source remains `.ai/agents/*`.
- Generated adapters must not replace canonical role contracts.
- `.ai/execution/adapter-role-mapping.md` is generation-time mapping.

## Required Loaded Contracts
Load all of the following before execution:
1. `AGENTS.md`
2. `INDEX.md`
3. `.ai/execution/runtime-adapter-contract.md`
4. `.ai/execution/adapter-role-mapping.md`
5. `.ai/execution/adapter-drift-validation.md`
6. `.ai/runtimes/claude/adapter-schema.md`
7. `.ai/runtimes/claude/tool-mapping.md`
8. Relevant canonical role source files from `.ai/agents/*` referenced by mapping
9. Relevant `.ai/workflows/*.md`
10. Relevant `.ai/policies/*.md`

## Inputs
- Explicit user request to build Claude adapters.
- Canonical role contracts from `.ai/agents/*`.
- Role mapping contract.
- Claude adapter schema contract.
- Claude tool mapping contract.
- Optional existing `.claude/agents/*.md` files for drift validation.

## Outputs
- `.claude/agents/*.md`
- `.ai/runtimes/claude/orchestration-bootstrap.md`
- `.ai/reports/claude-adapter-run-report.md`

## Default and Opt-In Roles
Default generated roles (must follow `.ai/execution/adapter-role-mapping.md`):
- `project-manager`
- `product-spec`
- `architect`
- `backend`
- `frontend`
- `tester` from canonical `qa`
- `reviewer` from canonical `code-review`
- `docs`

Opt-in only:
- `security`
- `devops`
- `database`
- specialized framework/runtime roles

## Generation Process
1. Read canonical role file.
2. Extract/summarize:
   - purpose
   - responsibilities
   - boundaries
   - escalation rules
   - validation/reporting expectations
3. Generate Claude YAML frontmatter:
   - `name`
   - task-oriented `description`
   - scoped `tools`
4. Generate adapter body:
   - generated notice
   - canonical source path
   - conflict rule
   - Claude-specific role summary
   - concise responsibilities
   - boundaries
   - escalation
5. Add generated metadata:
   - generated-by
   - generated-at
   - source path
   - source checksum/fingerprint
   - schema reference
6. Validate outputs.
7. Persist run report.

## Claude Adapter Content Rules
- Generated subagents must be useful for Claude automatic delegation.
- Do not generate pointer-only adapters.
- Do not duplicate full canonical role contracts.
- Keep descriptions concise and delegation-oriented.

## Validation Steps
1. Canonical source existence check.
2. Adapter output existence check.
3. Source fingerprint/checksum comparison.
4. Required metadata validation.
5. YAML frontmatter validation.
6. Required field validation (`name`, `description`).
7. Description quality validation (task-oriented and role-specific).
8. Canonical source pointer validation.
9. Minimal-content/no-full-duplication validation.
10. Tool-scope validation against least-privilege defaults.
11. Claude orchestration bootstrap presence/content validation.

## Rejection Conditions
Reject generation/validation when any apply:
- missing canonical source
- missing `name`
- missing or generic `description`
- missing canonical source pointer
- invalid YAML frontmatter
- full canonical role content copied into adapter
- unrestricted/all-tools default access without justification
- generated adapter conflicts with canonical `.ai/*`
- required metadata cannot be produced

## Report Persistence Rule
- Every `build-claude-agents` run must write a persistent report artifact file.
- Default report path: `.ai/reports/claude-adapter-run-report.md`.
- Final response summary does not replace the required report file artifact.

## Regeneration Invocation
Use this runtime command/prompt for regeneration:
- `Run .ai/workflows/build-claude-agents.md and generate .claude/agents/*.md from canonical .ai/agents/* using .ai/runtimes/claude/adapter-schema.md and .ai/runtimes/claude/tool-mapping.md, then write .ai/reports/claude-adapter-run-report.md.`
