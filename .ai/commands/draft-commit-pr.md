# Draft Commit + PR (canonical spec)

Single source of truth for drafting a Conventional Commit message, a PR title, and a PR body from staged changes. Both the `draft-commit-pr` command (text-only) and the `pr-manager` agent (executing) read this file so the format never drifts.

## Procedure

Inspect only STAGED changes (`git diff --staged --stat` then `git diff --staged`). If nothing is staged, report "No staged changes." and stop.

Determine new vs. modification: for each staged path run `git log --oneline -1 -- <path>`. Paths with no prior history are additions; paths with history are modifications. Pick the type from this — a genuinely new capability → `feat`; reworking existing code → `refactor`/`fix`/`chore`; docs-only → `docs`. Use the verb "add" only for genuinely new files.

### 1. Commit message — Conventional Commits
`<type>[optional scope]: <description>`
- type ∈ feat|fix|docs|style|refactor|perf|test|build|ci|chore|revert
- scope optional, derived from the touched area
- description: imperative mood, lowercase, ≤72 chars, no trailing period
- single-line subject only — do NOT add a body
- Do not add `Co-authored-by:` trailers. Human co-authors are permitted only when explicitly supplied by the user; AI runtime or harness co-authors are forbidden by the `ship-pr` guard.

### 2. PR title
Same Conventional Commit format as the commit subject.

### 3. PR body (markdown)
```
## Summary
<1–3 sentences covering what changed, why it changed, and the high-level implementation approach>
## Changes
- <bullet per meaningful change>
## Related
<issue/PR refs, or "None">
```

The `Summary` must answer what, why, and how at a high level. Keep detailed file-level changes under `Changes`; do not add nested headings or replace the required section structure.

## Output
Emit the commit message, the PR title, and the PR body in separate fenced blocks. Text only — this spec never runs `git commit`, `git push`, or opens a PR. Execution is owned by the `pr-manager` agent per `.ai/agents/runtime/pr-manager.md`.
