# Claude Tool Mapping

## Purpose
Define conservative default Claude Code tool access per generated project-level subagent.

## Scope
- Runtime adapter metadata only.
- Not a canonical role policy.
- Not a replacement for repository safety rules.
- Applies to the 16-role canonical set defined by `.ai/execution/adapter-role-mapping.md`.

## Default Mapping
```yaml
project-manager:
  - Read
  - Grep
  - Glob

project-owner:
  - Read
  - Grep
  - Glob

junior-project-manager:
  - Read
  - Edit
  - Grep
  - Glob

dev-team-lead:
  - Read
  - Edit
  - Grep
  - Glob

backend-developer:
  - Read
  - Edit
  - Bash
  - Grep
  - Glob

frontend-developer:
  - Read
  - Edit
  - Bash
  - Grep
  - Glob

database-administrator:
  - Read
  - Edit
  - Bash
  - Grep
  - Glob

devops-engineer:
  - Read
  - Edit
  - Bash
  - Grep
  - Glob

ui-ux-designer:
  - Read
  - Edit
  - Grep
  - Glob

web-designer:
  - Read
  - Edit
  - Bash
  - Grep
  - Glob

cybersecurity-analyst:
  - Read
  - Grep
  - Glob
  - Bash

qa-specialist:
  - Read
  - Edit
  - Bash
  - Grep
  - Glob

qa-team-lead:
  - Read
  - Grep
  - Glob
  - Bash

pr-manager:
  - Read
  - Edit
  - Bash
  - Grep
  - Glob

documentation-writer:
  - Read
  - Edit
  - Grep
  - Glob

doc-team-lead:
  - Read
  - Edit
  - Grep
  - Glob
```

## Rules
- Apply least-privilege by role.
- Do not default all agents to unrestricted/all-tools access.
- If broader access is needed for a role, document the role-specific reason in run output.

## Persona Inheritance Note
Persona files at `.ai/agents/personas/*.md` are read-only skill docs loaded on demand by `backend-developer`, `frontend-developer`, and `web-designer`. Personas do not have their own tool grants; the inheriting runtime role's tool grant applies.

## Notes
- Include `Bash` only where implementation, validation, or test execution reasonably requires it.
- `cybersecurity-analyst` and `qa-team-lead` include `Bash` for read-only validation commands (targeted tests, lint, diff-support checks) used to confirm findings before sign-off.
- `pr-manager` includes `Bash` for git operations involved in commit/PR handoff.
- Destructive commands remain governed by parent instructions and repository safety policies per `.ai/policies/approval-levels.md`.
- Tool mapping may be adjusted per project, but generated defaults should remain conservative.
