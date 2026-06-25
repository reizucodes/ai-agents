# Build Claude Agents Workflow

## Purpose
Define the Claude-specific workflow for generating and validating project-level subagent adapters from canonical runtime role contracts.

This workflow is a contract only. It does not generate files by itself. It defines how to generate `.claude/agents/*.md`. Adapters are generated only when this workflow is explicitly invoked. Repository installation, cloning, or first Claude Code execution must not generate adapters automatically.

## Scope
- Applies only when the user explicitly requests Claude adapter generation/validation work.
- Does not apply to normal feature, bugfix, refactor, review, or documentation tasks.

## Source-Repository Guard (Mandatory)
Before generating anything, check whether execution is happening in the ai-agents framework source repo.

**Primary detection:** `.ai/.framework-root` exists at repo root → this is the framework source repo.

If framework source is detected, stop and refuse generation by default. Do not create `.claude/agents/*` or `.ai/reports/claude-adapter-run-report.md`.

Required refusal response:
"Refusing to generate .claude/agents/* in the ai-agents source repository. Import the framework into a consumer project first, then run build-claude-agents there."

**Override (unsafe/diagnostic only):** Generation inside the framework source repo is allowed only when the user explicitly states `override: generate claude agents in source repo`. Without that exact override intent, generation remains blocked.

## Canonical and Adapter Clarifications
- `AGENTS.md` and `.ai/*` remain canonical.
- `.claude/agents/*.md` are generated Claude runtime adapters only.
- Canonical adapter sources are `.ai/agents/runtime/*.md` (exactly 16 files).
- `.ai/agents/personas/*.md` are NEVER adapter sources. They are inheritance-only skill docs loaded on demand by matching runtime agents.
- Generated adapters are build artifacts and must not replace canonical role contracts.
- `.ai/execution/adapter-role-mapping.md` is the authoritative 16-role list.

## Required Loaded Contracts
Load all of the following before execution:
1. `AGENTS.md`
2. `INDEX.md`
3. `.ai/execution/runtime-adapter-contract.md`
4. `.ai/execution/adapter-role-mapping.md`
5. `.ai/execution/adapter-drift-validation.md`
6. `.ai/runtimes/claude/adapter-schema.md`
7. `.ai/runtimes/claude/tool-mapping.md`
8. `.ai/policies/approval-levels.md`
9. `.ai/policies/quality-gates.md`
10. `.ai/policies/risk-classification.md`
11. The 16 canonical role source files from `.ai/agents/runtime/*.md`
12. Persona files at `.ai/agents/personas/*.md` (used only to describe inheritance, not as adapter sources)

## Inputs
- Explicit user request to build Claude adapters.
- Canonical runtime role contracts from `.ai/agents/runtime/*`.
- The 16-role list and persona-inheritance map in `.ai/execution/adapter-role-mapping.md`.
- Claude adapter schema (`.ai/runtimes/claude/adapter-schema.md`).
- Claude tool mapping (`.ai/runtimes/claude/tool-mapping.md`).
- Optional existing `.claude/agents/*.md` files for drift validation.

## Outputs
- Exactly 16 adapter files at `.claude/agents/<role>.md`, with filenames matching the canonical runtime filenames listed in `adapter-role-mapping.md`.
- `.ai/runtimes/claude/orchestration-bootstrap.md` (refreshed when role set or routing rules change).
- `.ai/reports/claude-adapter-run-report.md`.

## Canonical Output Set (16)
The 16 adapter filenames are fixed by `.ai/execution/adapter-role-mapping.md`:
`backend-developer`, `cybersecurity-analyst`, `database-administrator`, `dev-team-lead`, `devops-engineer`, `doc-team-lead`, `documentation-writer`, `frontend-developer`, `junior-project-manager`, `pr-manager`, `project-manager`, `project-owner`, `qa-specialist`, `qa-team-lead`, `ui-ux-designer`, `web-designer`.

`project-manager` is the primary/orchestrator adapter. The other 15 are subagents.

Build workflow MUST refuse to generate any adapter whose name is not in this list. Build workflow MUST NOT generate adapters from `.ai/agents/personas/*`.

## Persona Inheritance Documentation
For the three inheriting roles, the generated adapter's body must describe how the agent loads persona files on demand when task detection matches a stack:

- `backend-developer`: when working in a Laravel/PHP, FastAPI/Python, Node/Express, or generic Python backend, load `.ai/agents/personas/<stack>.md` for stack-specific patterns, conventions, and idioms before performing the change.
- `frontend-developer`: when working in a React or Vue codebase, load `.ai/agents/personas/<stack>.md`.
- `web-designer`: when the surface renders through React or Vue, load `.ai/agents/personas/<stack>.md`.

Persona inheritance is documented in the adapter prompt as a directive ("load `.ai/agents/personas/<stack>.md` on demand when stack X is detected"). Persona files are NEVER copied into the adapter body and are NEVER emitted as `.claude/agents/*.md`.

## Generation Process
For each of the 16 canonical roles listed in `adapter-role-mapping.md`:
1. Resolve canonical source path `.ai/agents/runtime/<name>.md`.
2. Extract/summarize:
   - role identity and purpose
   - responsibilities
   - ownership boundaries
   - delegation triggers
   - escalation rules
   - deliverables and required artifacts
   - phase participation (per `adapter-role-mapping.md`)
3. Generate Claude YAML frontmatter per `.ai/runtimes/claude/adapter-schema.md`:
   - `name` (matches canonical filename without `.md`)
   - task-oriented `description` (delegation-critical)
   - scoped `tools` per `.ai/runtimes/claude/tool-mapping.md`
4. Generate adapter body:
   - generated adapter notice
   - explicit child-scope guard: worker is not the main session
   - canonical source pointer (`.ai/agents/runtime/<name>.md`)
   - explicit instruction to read `.ai/agents/runtime/<name>.md` before performing role work
   - canonical conflict rule
   - explicit statement that role-defined work is allowed within assigned scope and policy gates
   - role summary, responsibilities, boundaries, escalation, deliverables
   - delegation contract (worker owns delegated scope, does not silently return ownership)
   - persona inheritance directive (only for `backend-developer`, `frontend-developer`, `web-designer`)
   - references to surviving policies: `.ai/policies/approval-levels.md`, `.ai/policies/quality-gates.md`, `.ai/policies/risk-classification.md`, `.ai/policies/definition-of-done.md`, `.ai/policies/secrets-management.md`
5. Add generated metadata block (HTML comment preferred) per `.ai/runtimes/claude/adapter-schema.md`:
   - `generated-by`
   - `generated-at` (UTC ISO-8601)
   - `canonical-source`
   - `canonical-source-fingerprint` (`sha256:<hex>`)
   - `schema-ref: .ai/runtimes/claude/adapter-schema.md`
6. Validate outputs (see Validation Steps).
7. Persist run report.

## Approval and Gate Model
Generated adapters must reference the 3-gate model defined in `.ai/policies/approval-levels.md`:
- **Auto**: runtime proceeds without human input.
- **Recommendation**: present options, default to safe path, log decision.
- **Approval**: halt and require explicit user approval before proceeding.

Adapters must not invent additional gate types or bypass the approval-levels contract.

## Claude Adapter Content Rules
- Generated subagents must be useful for Claude automatic delegation.
- Generated subagents must be immediately callable as the default specialist workers when present.
- Generated subagents must state that parent/main must delegate matching work to them.
- Generated subagents must state that they own the delegated implementation scope and must not silently return implementation ownership to parent/main.
- Generated subagents must state that they are delegated child agents, not the main session.
- Generated subagents must require reading `.ai/agents/runtime/<role>.md` before role work.
- Generated subagents must state that role-defined work is allowed within assigned scope and policy gates.
- Do not generate pointer-only adapters.
- Do not duplicate the full canonical role contract.
- Keep descriptions concise and delegation-oriented.

## Validation Steps
1. Role-set integrity: exactly 16 outputs, names match `adapter-role-mapping.md`.
2. No outputs derived from `.ai/agents/personas/*`.
3. Canonical source existence check for each adapter (`.ai/agents/runtime/<name>.md` exists).
4. Adapter output existence check.
5. Source fingerprint/checksum comparison (`sha256:<hex>`).
6. Required metadata block present and well-formed.
7. YAML frontmatter valid; `name` is lowercase kebab-case matching canonical filename.
8. `description` is concise, task-oriented, role-specific.
9. `tools` scoped per `.ai/runtimes/claude/tool-mapping.md`.
10. Canonical source pointer present.
11. No full canonical-role duplication.
12. Persona inheritance directive present for `backend-developer`, `frontend-developer`, `web-designer`.
13. Claude orchestration bootstrap presence/content validation.
14. Child-scope guard present: adapter says it is not the main session.
15. Canonical pre-read directive present.
16. Role-defined-work authorization present and policy-gated.

## Rejection Conditions
Reject generation/validation when any apply:
- `.ai/.framework-root` present at repo root without explicit override phrase
- adapter name not in canonical 16-role set
- attempt to generate an adapter from `.ai/agents/personas/*`
- missing canonical source at `.ai/agents/runtime/<name>.md`
- missing `name` or missing/generic `description`
- missing canonical source pointer
- invalid YAML frontmatter
- full canonical role content copied into adapter
- unrestricted/all-tools access without documented justification
- generated adapter conflicts with canonical `.ai/*`
- required metadata cannot be produced

## Report Persistence Rule
- Every `build-claude-agents` run must write a persistent report artifact file.
- Default report path: `.ai/reports/claude-adapter-run-report.md`.
- Final response summary does not replace the required report file artifact.

## Regeneration Invocation
Use this runtime command/prompt for regeneration:
- `Run .ai/workflows/build-claude-agents.md and generate the 16 adapters at .claude/agents/*.md from canonical .ai/agents/runtime/* using .ai/execution/adapter-role-mapping.md, .ai/runtimes/claude/adapter-schema.md, and .ai/runtimes/claude/tool-mapping.md, then write .ai/reports/claude-adapter-run-report.md.`
