---
description: Draft, confirm, then commit staged changes and open a PR to a target branch (defaults to development) via the pr-manager agent
---

Target branch: $ARGUMENTS

Follow `.ai/commands/ship-pr.md`. Delegate to the `pr-manager` agent. If `$ARGUMENTS` is empty, default the target base to `development`. ALWAYS confirm the target branch with a hard approval gate before committing — even on the default. If the `pr-manager` adapter is unavailable, disclose the gap and halt unless the user says `no subagent` / `main only`.
