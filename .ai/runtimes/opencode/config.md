# OpenCode Config Mapping

## Root Config
- Repository uses committed `opencode.json` at project root.
- `opencode.json` is runtime config, not bootstrap output.

## Instruction Loading
OpenCode `instructions` includes canonical instruction paths so AGENTS-level references are explicit and deterministic.

## Default Agent Decision
- `default_agent` is set to `project-manager`.
- Rationale: repository orchestration entrypoint starts with planning/orchestration roles before implementation in Medium/Large flows (`project-manager -> product-spec -> architect`).
- `project-manager` is configured as a `primary` OpenCode agent so it is valid for `default_agent`.

## OpenCode-Specific Notes
- OpenCode requires `default_agent` to be a primary agent.
- If `default_agent` is invalid, OpenCode falls back to `build` with warning.
