# Claude Adapter Schema Contract

## Purpose
Define the Claude Code adapter contract for generating derivative project-level subagents from canonical `.ai/agents/*` role contracts.

## Scope
- Applies only to Claude runtime adapter generation and validation tasks.
- Applies to project-level Claude subagents and Claude orchestration bootstrap guidance.
- Does not apply to normal feature, bugfix, refactor, review, or release execution.

## Canonical Source Rule
- `AGENTS.md` and `.ai/*` are canonical.
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

## Example Generated Shape
```md
---
name: backend
description: Use for backend implementation, APIs, persistence, services, repositories, and backend refactors.
tools:
  - Read
  - Edit
  - Bash
---

Generated Claude Code subagent adapter.

Canonical source:
.ai/agents/backend.md

Follow the canonical role contract before performing role work.

This adapter is not the source of truth.
If this adapter conflicts with the canonical role contract, follow the canonical role contract.

## Role Summary
Backend implementation specialist for API, service, repository, and persistence work.

## Responsibilities
- Implement backend changes within approved architecture.
- Maintain service/repository boundaries.
- Keep APIs testable and maintainable.

## Boundaries
- Do not redefine architecture.
- Escalate architectural decisions to architect.
- Do not silently change product scope.

## Escalation
Use architect for architecture changes.
Use tester for validation coverage.
Use reviewer for final code review.
Use docs for persisted run reports.
```
