# Codex Nickname Strategy Contract

## Purpose
Define optional nickname metadata behavior for Codex custom-agent adapters.

## Scope
- Applies only to Codex adapter-generation tasks.
- Does not apply to normal execution tasks.

## Core Rules
- `nickname_candidates` is required for each generated adapter role in this strategy.
- Stable adapter `name` is the only authoritative routing identifier.
- `nickname_candidates` are advisory metadata only.
- Runtime may ignore `nickname_candidates` and assign display names independently.
- Nicknames are UI/readability labels only.
- Nicknames must not be used as canonical role names.
- Do not depend on nicknames for orchestration, validation, reporting, or handoffs.
- Include `nickname_candidates` only for forward compatibility when supported by Codex.
- Display-name contract for generated adapters and traces/logs:
  - `<nickname> [<canonical-role>]`
  - Example: `Canvas [frontend]`.
- Canonical role suffix (`[<canonical-role>]`) is mandatory in display form.
- Runtime nickname alone is never authoritative.
- If runtime does not honor configured nickname selection, adapter instructions must still require self-identification as:
  - `<runtime-nickname-or-configured-nickname> [<canonical-role>]`.

## Recommended Candidates
| Adapter | nickname_candidates |
|---|---|
| project-manager | `["Marshal", "Beacon"]` |
| product-spec | `["Scribe", "Brief"]` |
| architect | `["Forge", "Keystone"]` |
| backend | `["Anchor", "Iron"]` |
| frontend | `["Canvas", "Prism"]` |
| tester | `["Scout", "Pulse"]` |
| reviewer | `["Audit", "Ledger"]` |
| security | `["Cipher", "Shield"]` |
| devops | `["Relay", "Harbor"]` |
| database | `["Atlas", "Archive"]` |
| documentation | `["Quill", "Echo"]` |

## Collision and Quality Rules
- Duplicate nickname within one generated adapter set is disallowed.
- Duplicate display label within one active spawned-child set is disallowed.
- If collision occurs, replace with role-distinct alternatives.
- Nicknames may be stable across regenerations, but runtime display names are not guaranteed.
- Weak nickname quality (ambiguous, role-confusing, duplicate-prone) should trigger replacement.

## Reporting and Handoff Rule
- Parent and child agents must report canonical adapter `name` and canonical role path.
- Runtime display nicknames must be treated as non-authoritative UI labels.
- Agent traces/logs should prefer the display format `<nickname> [<canonical-role>]`.
