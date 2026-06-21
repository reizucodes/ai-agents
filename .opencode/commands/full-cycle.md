---
description: Run canonical end-to-end workflow with orchestration and gated delegation.
---

Task: $ARGUMENTS

Use canonical sources only:
- `AGENTS.md`
- `INDEX.md`
- `.ai/workflows/feature.md` (or `.ai/workflows/bugfix.md` when defect-driven)
- `.ai/policies/*.md`
- `.ai/execution/*.md`
- `.ai/delegation/*.md`

Route through canonical stages and preserve proposal/approval gates.

If generated OpenCode agents exist under `.opencode/agents/*`, use them.
If required agents do not exist, disclose the missing adapter(s) by name, halt, and await explicit user instruction (`no subagent` / `main only`).
