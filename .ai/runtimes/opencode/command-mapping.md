# OpenCode Command Mapping

## Canonical Source
Commands map to canonical workflows and policies.
Generated workers are the 16-role canonical runtime set from `.ai/agents/runtime/*.md` (see `.ai/execution/adapter-role-mapping.md`).

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
- Command body should route to generated/native workers when present because they are the default specialist workers.
- If a required worker is absent, disclose the gap and obtain explicit approval before parent-only fallback unless the user explicitly says `no subagent` or `main only`.
- Do not copy workflow logic into command files.
- Natural invocation `build opencode agents` should be handled by `.opencode/commands/build-opencode-agents.md`.
- The `build-opencode-agents` command must route to `.ai/workflows/build-opencode-agents.md` and generate the 16 canonical static markdown adapters only.
- Persona files at `.ai/agents/personas/*.md` are NEVER targeted by command-mapped generation.
