# OpenCode Workflow Mapping

## Canonical Source
Canonical workflows are defined in `.ai/workflows/*.md`.

## Runtime Mapping
- OpenCode commands and agent prompts invoke canonical workflows by reference.
- Workflow logic is not duplicated into `.opencode/commands/*`.

## Core Mappings
- `feature` work -> `.ai/workflows/feature.md`
- `bugfix` work -> `.ai/workflows/bugfix.md`
- `refactor` work -> `.ai/workflows/refactor.md`
- `release` work -> `.ai/workflows/release.md`
- `runtime adapter generation` work -> relevant runtime workflow documents only when explicitly requested

## Governance
All workflow runs remain bound by `.ai/policies/*`, `.ai/execution/*`, and `.ai/delegation/*`.
