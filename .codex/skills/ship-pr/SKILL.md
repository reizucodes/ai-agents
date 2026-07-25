---
name: ship-pr
description: Draft, confirm, then commit staged changes and open a PR to a target branch (defaults to development) via the pr-manager agent. Trigger when the user wants to ship/open a PR from staged changes.
---

Follow `.ai/commands/ship-pr.md`. Delegate to the pr-manager agent.

The target branch is provided via `$ARGUMENTS`. If no target branch is given, default the target base to `development`.

ALWAYS confirm the target branch with a hard approval gate before committing — even on the default.

If the pr-manager adapter is unavailable, disclose the gap and halt unless the user says `no subagent` / `main only`.
