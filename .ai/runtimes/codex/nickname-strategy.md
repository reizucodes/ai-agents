# Codex Nickname Strategy Contract

## Purpose
Define optional nickname metadata behavior for Codex custom-agent adapters.

## Scope
- Applies only to Codex adapter-generation tasks.
- Does not apply to normal execution tasks.

## Core Rules
- `nickname_candidates` is optional.
- Stable adapter `name` is the only authoritative routing identifier.
- `nickname_candidates` are advisory metadata only.
- Runtime may ignore `nickname_candidates` and assign display names independently.
- Nicknames are UI/readability labels only.
- Nicknames must not be used as canonical role names.
- Do not depend on nicknames for orchestration, validation, reporting, or handoffs.
- Include `nickname_candidates` only for forward compatibility when supported by Codex.
- When runtime supports display labels, prefer role-tag format:
  - `<nickname> [<canonical-role>]`
  - Example: `Marcus [frontend]`.

## Recommended Candidates
| Adapter | nickname_candidates |
|---|---|
| project-manager | `["Drucker", "Mintzberg"]` |
| product-spec | `["Parnas", "Constantine"]` |
| architect | `["Brooks", "Vitruvius"]` |
| backend | `["Turing", "Dijkstra"]` |
| frontend | `["Lovelace", "Berners-Lee"]` |
| tester | `["Hopper", "Lamport"]` |
| reviewer | `["Knuth", "McIlroy"]` |
| security | `["Diffie", "Shamir"]` |
| devops | `["Borg", "Phoenix"]` |
| database | `["Codd", "Date"]` |
| docs | `["Engelbart", "Spolsky"]` |

## Collision and Quality Rules
- Duplicate nickname within one generated adapter set is disallowed.
- Duplicate display label within one active spawned-child set is disallowed.
- If collision occurs, replace with role-distinct alternatives.
- Nicknames may be stable across regenerations, but runtime display names are not guaranteed.
- Weak nickname quality (ambiguous, role-confusing, duplicate-prone) should trigger replacement.

## Reporting and Handoff Rule
- Parent and child agents must report canonical adapter `name` and canonical role path.
- Runtime display nicknames must be treated as non-authoritative UI labels.
