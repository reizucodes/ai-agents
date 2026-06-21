---
description: Perform canonical code review workflow for correctness and risk.
---

Scope: $ARGUMENTS

Use `.ai/agents/runtime/pr-manager.md` and relevant `.ai/policies/*`.
Focus on correctness, maintainability, security, performance, and test depth.

If generated OpenCode agents exist under `.opencode/agents/*`, use them.
If required agents do not exist, disclose the missing adapter(s) by name, halt, and await explicit user instruction (`no subagent` / `main only`).
