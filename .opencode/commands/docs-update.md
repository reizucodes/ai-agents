---
description: Update run artifacts and canonical documentation after validated changes.
---

Scope: $ARGUMENTS

Use canonical docs workflow/contracts:
- `.ai/agents/docs.md`
- `.ai/templates/*.md`
- `.ai/policies/definition-of-done.md`

Update only documentation and reporting artifacts tied to completed work.

If generated OpenCode agents exist under `.opencode/agents/*`, use them.
If generated agents do not exist, execute using canonical workflows and runtime instructions.
