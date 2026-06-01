# OpenCode Command Mapping

## Canonical Source
Commands map to canonical workflows and policies.

## Runtime Command Files
- `.opencode/commands/build-opencode-agents.md`
- `.opencode/commands/full-cycle.md`
- `.opencode/commands/spec-first.md`
- `.opencode/commands/regression-review.md`
- `.opencode/commands/review.md`
- `.opencode/commands/docs-update.md`

## Mapping Rules
- Commands must remain startup-safe in source repositories where `.opencode/agents/*` is absent.
- Do not require generated-agent frontmatter (`agent`, `subtask`) in committed command files.
- Command body references canonical workflow/policy files.
- Command body may use generated agents when present and fall back to canonical runtime execution when absent.
- Do not copy workflow logic into command files.
- Natural invocation `build opencode agents` should be handled by `.opencode/commands/build-opencode-agents.md`.
- The `build-opencode-agents` command must route to `.ai/workflows/build-opencode-agents.md` and generate static markdown adapters only.
