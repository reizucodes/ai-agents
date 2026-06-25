# Claude Adapter Schema Contract

## Purpose
Define the Claude Code adapter contract for generating derivative project-level subagents from canonical `.ai/agents/*` role contracts.

## Scope
- Applies only to Claude runtime adapter generation and validation tasks.
- Applies to project-level Claude subagents and Claude orchestration bootstrap guidance.
- Does not apply to normal feature, bugfix, refactor, review, or release execution.

## Canonical Source Rule
- `AGENTS.md` and `.ai/*` are canonical.
- Claude adapter sources are `.ai/agents/runtime/*.md` (exactly 16 files; see `.ai/execution/adapter-role-mapping.md`).
- `.ai/agents/personas/*.md` are NEVER adapter sources; they are inheritance-only skill docs.
- Claude adapter artifacts are derivative runtime outputs only.
- If generated Claude adapter content conflicts with canonical `.ai/*`, canonical `.ai/*` wins.
- Canonical role definitions must not be rewritten during adapter generation.

## Output Paths
- Project-level subagent output: `.claude/agents/*.md`
- Claude orchestration bootstrap output: `.ai/runtimes/claude/orchestration-bootstrap.md`

## Out Of Scope
- User-level agents under `~/.claude/agents/`

## Required YAML Frontmatter
Each generated Claude subagent file must include:
- `name`
- `description`

## Recommended YAML Frontmatter
- `tools` should be explicitly scoped by role unless a documented reason exists to omit it.

## Optional YAML Frontmatter
- `model`
- `color`

## Generated Metadata Requirements
Each generated subagent must include drift-checkable metadata. Metadata representation may use one of:
- YAML frontmatter keys
- visible `## Generated Metadata` section
- machine-readable HTML comment metadata block

Preferred current convention:
- Use an HTML comment metadata block so operational metadata remains machine-readable without cluttering rendered agent instructions.

Required metadata fields:
- generated-by marker
- generated-at timestamp (UTC ISO-8601)
- canonical-source
- canonical-fingerprint using SHA-256 notation `sha256:<hex-digest>`
- schema-ref (`.ai/runtimes/claude/adapter-schema.md`)

## Body Requirements
Adapter body must include:
- generated adapter notice
- canonical source pointer
- canonical conflict rule
- explicit statement that the worker is a delegated child agent, not the main session
- explicit instruction to read `.ai/agents/runtime/<role>.md` before performing role work
- explicit statement that role-defined work is allowed within assigned scope and policy gates
- short Claude-specific role summary
- concise responsibilities
- boundaries
- escalation rules
- validation/reporting expectations when applicable

Body constraints:
- Keep adapter content concise and delegation-oriented.
- Do not duplicate the full canonical role contract.
- Do not generate pointer-only adapters.

## Claude Delegation Rule
- `description` is delegation-critical and should be concise, task-oriented, and role-specific.
- `description` should help Claude route work to the correct subagent automatically.

## Validation Rules
1. Markdown has valid YAML frontmatter.
2. `name` is lowercase kebab-case.
3. `description` is concise, task-oriented, and role-specific.
4. `tools` is explicit unless a documented reason is present.
  5. Body includes canonical source pointer.
6. Body does not duplicate full canonical role content.
7. Generated metadata is present and drift-checkable.
8. Canonical conflict rule is present.
9. File path is under `.claude/agents/`.
10. Body states the child is not the main session.
11. Body requires reading `.ai/agents/runtime/<role>.md` before role work.
12. Body states that role-defined work is allowed within assigned scope and policy gates.

## Persona Inheritance
For `backend-developer`, `frontend-developer`, and `web-designer`, the adapter body must include a directive that the agent loads `.ai/agents/personas/<stack>.md` on demand when the matching stack is detected:
- `backend-developer`: laravel, fastapi, node-express, python
- `frontend-developer`: react, vue
- `web-designer`: react, vue (when surface renders through a JS framework)

Persona files are never copied into the adapter and never generated as `.claude/agents/*.md`.

## Example Generated Shape
```md
---
name: backend-developer
description: Use for backend implementation, APIs, services, persistence, repositories, and backend refactors. Loads stack persona on demand.
tools:
  - Read
  - Edit
  - Bash
---

Generated Claude Code subagent adapter.

You are a delegated child agent, not the main session.

Canonical source:
.ai/agents/runtime/backend-developer.md

Follow the canonical role contract before performing role work.
Before performing role work, read and follow `.ai/agents/runtime/backend-developer.md`.

This adapter is not the source of truth.
If this adapter conflicts with the canonical role contract, follow the canonical role contract.

You may execute the work your role and assigned scope require, subject to `.ai/policies/approval-levels.md` and other applicable policies.

## Role Summary
Backend implementation specialist for API, service, repository, and persistence work under approved technical-approach boundaries from `dev-team-lead`.

## Responsibilities
- Implement backend changes within approved technical approach.
- Maintain service/repository boundaries.
- Keep APIs testable and maintainable.

## Persona Inheritance
When the backend stack is detected, load the matching persona on demand:
- Laravel/PHP -> `.ai/agents/personas/laravel.md`
- FastAPI/Python web -> `.ai/agents/personas/fastapi.md`
- Node/Express -> `.ai/agents/personas/node-express.md`
- Generic Python -> `.ai/agents/personas/python.md`

## Boundaries
- Do not redefine technical approach.
- Escalate architecture/contract decisions to `dev-team-lead`.
- Do not silently change product scope; escalate to `project-owner` via `project-manager`.

## Escalation
- `dev-team-lead` for technical-approach changes.
- `qa-specialist` / `qa-team-lead` for validation coverage.
- `pr-manager` for commit/PR handoff.
- `documentation-writer` / `doc-team-lead` for doc updates.
```
