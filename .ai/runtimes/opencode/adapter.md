# OpenCode Adapter Contract

## Scope
This contract governs OpenCode runtime integration only.

## Adapter Model
- OpenCode reads `AGENTS.md` and merges additional files from `opencode.json.instructions`.
- OpenCode runtime agents and commands are adapters to canonical `.ai/*` definitions.
- Adapter assets must reference canonical files and avoid full-definition duplication.

## Canonical Preservation
- Do not rewrite `.ai/agents/runtime/*`, `.ai/agents/personas/*`, `.ai/workflows/*`, `.ai/policies/*`, `.ai/execution/*`, `.ai/delegation/*`, `.ai/templates/*` for runtime-specific behavior.
- Runtime-specific behavior belongs in `.ai/runtimes/opencode/*` and `.opencode/commands/*` in this source repo.
- OpenCode agent adapters are generated artifacts under consumer-project `.opencode/agents/*` and are derived only from the 16 canonical runtime roles at `.ai/agents/runtime/*.md` (see `.ai/execution/adapter-role-mapping.md`).
- Persona files at `.ai/agents/personas/*.md` are inheritance-only and never generated as runtime adapters.

## Conflict Rule
If OpenCode adapter behavior conflicts with canonical governance or workflow contracts, follow canonical contracts and fix adapter files.
