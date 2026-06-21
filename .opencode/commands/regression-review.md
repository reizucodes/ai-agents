---
description: Run regression-focused QA and code review on a scoped change.
---

Scope: $ARGUMENTS

Use canonical validation and review contracts:
- `.ai/agents/runtime/qa-specialist.md`
- `.ai/agents/runtime/qa-team-lead.md`
- `.ai/agents/runtime/pr-manager.md`
- `.ai/policies/quality-gates.md`
- `.ai/policies/definition-of-done.md`

Report regressions, coverage gaps, and remediation actions.

If generated OpenCode agents exist under `.opencode/agents/*`, use them.
If required agents do not exist, disclose the missing adapter(s) by name, halt, and await explicit user instruction (`no subagent` / `main only`).
