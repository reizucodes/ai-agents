# OpenCode Workflow Mapping

## Canonical Source
Canonical workflows are defined in `.ai/workflows/*.md`.
Canonical runtime roles are defined in `.ai/agents/runtime/*.md` (the 16-role set in `.ai/execution/adapter-role-mapping.md`).
Persona/skill docs live at `.ai/agents/personas/*.md` (inheritance-only).

## Runtime Mapping
- OpenCode commands and agent prompts invoke canonical workflows by reference.
- Workflow logic is not duplicated into `.opencode/commands/*`.

## Core Mappings
- `feature` work -> `.ai/workflows/feature.md`
- `bugfix` work -> `.ai/workflows/bugfix.md`
- `refactor` work -> `.ai/workflows/refactor.md`
- `release` work -> `.ai/workflows/release.md`
- `runtime adapter generation` work -> `.ai/workflows/build-opencode-agents.md` (only when explicitly requested)

## Governance
All workflow runs remain bound by `.ai/policies/*`, `.ai/execution/*`, and `.ai/delegation/*`. Approval gates follow the three-gate model in `.ai/policies/approval-levels.md` (Auto / Recommendation / Approval).
