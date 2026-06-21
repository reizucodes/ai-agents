---
description: Run discovery and planning-first flow before implementation.
---

Task: $ARGUMENTS

Execute spec-first path using canonical contracts:
- `.ai/agents/runtime/project-owner.md`
- `.ai/agents/runtime/dev-team-lead.md`
- `.ai/templates/proposal-review-package.md`
- `.ai/policies/approval-levels.md`

Do not start implementation until required proposal artifacts exist and explicit approval is confirmed.

If generated OpenCode agents exist under `.opencode/agents/*`, use them.
If required agents do not exist, disclose the missing adapter(s) by name, halt, and await explicit user instruction (`no subagent` / `main only`).
