---
description: Update run artifacts and canonical documentation after validated changes.
---

Scope: $ARGUMENTS

Use canonical docs workflow/contracts:
- `.ai/agents/runtime/documentation-writer.md`
- `.ai/agents/runtime/doc-team-lead.md`
- `.ai/templates/*.md`
- `.ai/policies/definition-of-done.md`

Update only documentation and reporting artifacts tied to completed work.

If generated OpenCode agents exist under `.opencode/agents/*`, use them.
If required agents do not exist, disclose the missing adapter(s) by name, halt, and await explicit user instruction (`no subagent` / `main only`).
