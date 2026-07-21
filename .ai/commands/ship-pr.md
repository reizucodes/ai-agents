# Ship PR (canonical spec)

Executing counterpart to `draft-commit-pr`. Delegates to the `pr-manager` agent to draft, confirm, then commit staged changes and open a PR to a target branch.

## Input
- Target branch = first argument. If omitted, default to `development`.

## Delegation
The main session MUST delegate to the `pr-manager` agent (`.ai/agents/runtime/pr-manager.md`). If the generated `pr-manager` adapter is unavailable, disclose the gap and halt (unless the user says `no subagent` / `main only`).

## Prerequisites
- `gh` (GitHub CLI) installed and authenticated. If unavailable, do NOT hard-fail silently — see step 1a fallback.

## Procedure (owned by pr-manager)
1. Draft the commit subject, PR title, and PR body using `.ai/commands/draft-commit-pr.md` against `git diff --staged`. If nothing is staged, report and stop.
1a. Check `gh` is installed and authenticated. If not: report the error, print the drafted commit message, PR title, and PR body so the user can commit/open the PR manually, then stop before any state change.
2. Same-branch guard — if the current branch equals the target base, halt: "You're on `<branch>`. Switch to a feature branch before shipping a PR to `<target>`." Do not commit.
3. Present the draft.
4. HARD approval gate — ALWAYS confirm the target branch before any state change, even when it is the default `development`. Show: target base branch, current branch, commit subject. Wait for explicit user confirmation.
5. On confirmation, execute in order (each state-changing git/gh call surfaces its own approval prompt per `.ai/policies/approval-levels.md` — L1 commit, L2 push). Push to the current branch's remote (default `origin` if none configured):
   - `git commit -m "<subject>"`
   - `git push -u <remote> <current-branch>`
   - `gh pr create --base <target> --title "<title>" --body "<body>"`
6. Return the PR URL.

## Guardrails
- Never push to or open a PR against a branch the user has not confirmed in step 4.
- Never open a PR from a branch to itself (see step 2).
- Never force-push. Never commit unstaged changes.
