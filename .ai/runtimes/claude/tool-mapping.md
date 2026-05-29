# Claude Tool Mapping

## Purpose
Define conservative default Claude Code tool access per generated project-level subagent.

## Scope
- Runtime adapter metadata only.
- Not a canonical role policy.
- Not a replacement for repository safety rules.

## Default Mapping
```yaml
project-manager:
  - Read
  - Grep
  - Glob

product-spec:
  - Read
  - Edit
  - Grep
  - Glob

architect:
  - Read
  - Edit
  - Grep
  - Glob

backend:
  - Read
  - Edit
  - Bash
  - Grep
  - Glob

frontend:
  - Read
  - Edit
  - Bash
  - Grep
  - Glob

tester:
  - Read
  - Edit
  - Bash
  - Grep
  - Glob

reviewer:
  - Read
  - Grep
  - Glob
  - Bash

docs:
  - Read
  - Edit
  - Grep
  - Glob
```

## Rules
- Apply least-privilege by role.
- Do not default all agents to unrestricted/all-tools access.
- If broader access is needed for a role, document the role-specific reason in run output.

## Notes
- Include `Bash` only where implementation, validation, or test execution reasonably requires it.
- `reviewer` includes `Bash` for read-only validation commands (for example running targeted tests, lint, and diff-support checks) used to confirm findings before sign-off.
- Destructive commands remain governed by parent instructions and repository safety policies.
- Tool mapping may be adjusted per project, but generated defaults should remain conservative.
