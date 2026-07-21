# Draft Commit + PR (canonical spec)

Single source of truth for drafting a Conventional Commit message, a PR title, and a PR body from staged changes. Both the `draft-commit-pr` command (text-only) and the `pr-manager` agent (executing) read this file so the format never drifts.

## Procedure

Inspect only STAGED changes (`git diff --staged --stat` then `git diff --staged`). If nothing is staged, report "No staged changes." and stop.

### 1. Commit message — Conventional Commits
`<type>[optional scope]: <description>`
- type ∈ feat|fix|docs|style|refactor|perf|test|build|ci|chore|revert
- scope optional, derived from the touched area
- description: imperative mood, lowercase, ≤72 chars, no trailing period
- single-line subject only — do NOT add a body

### 2. PR title
Same Conventional Commit format as the commit subject.

### 3. PR body (markdown)
```
## Summary
<1–3 sentences on why>
## Changes
- <bullet per meaningful change>
## Related
<issue/PR refs, or "None">
```

## Output
Emit the commit message, the PR title, and the PR body in separate fenced blocks. Text only — this spec never runs `git commit`, `git push`, or opens a PR. Execution is owned by the `pr-manager` agent per `.ai/agents/runtime/pr-manager.md`.
