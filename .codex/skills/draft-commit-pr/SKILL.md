---
name: draft-commit-pr
description: Draft a Conventional Commit message, PR title, and PR body from staged changes. Text only — does not commit, push, or open a PR. Trigger when the user wants to prepare/draft a commit message or PR copy for already-staged changes.
---

Read `.ai/commands/draft-commit-pr.md` and follow it exactly.

If the user provides an optional hint (via `$ARGUMENTS`), use it to bias the type/scope/description of the drafted commit.

Output text only. Do NOT run `git commit`, `git push`, or create a PR.
