---
description: Draft a Conventional Commit message and PR markdown from staged changes (text only, does not commit or push)
---

Optional hint: $ARGUMENTS

Inspect only STAGED changes (`git diff --staged --stat` then `git diff --staged`). If nothing is staged, say "No staged changes." and stop.

1. Commit message — Conventional Commits:
   `<type>[optional scope]: <description>`
   - type ∈ feat|fix|docs|style|refactor|perf|test|build|ci|chore|revert
   - scope optional, derived from the touched area
   - description: imperative mood, lowercase, ≤72 chars, no trailing period
   - single-line subject only — do NOT add a body

2. PR markdown:
   ```
   ## Summary
   <1–3 sentences on why>
   ## Changes
   - <bullet per meaningful change>
   ## Related
   <issue/PR refs, or "None">
   ```

If a hint is provided, use it to bias the type/scope/description.

Output the commit message and the PR markdown in separate fenced blocks. Do NOT run `git commit`, `git push`, or create a PR — output text only.
