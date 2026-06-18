# Adapter Role Mapping Contract

## Purpose
This file is the single canonical source of the 16-role list used by all runtime adapter generation workflows (`build-claude-agents`, `build-codex-agents`, `build-opencode-agents`). Every build workflow MUST consume this file as its authoritative role list. Build workflows MUST NOT generate adapters from `.ai/agents/personas/*` — those files are inheritance-only skill docs read on demand by matching runtime agents.

## Scope
- Applies only to runtime adapter generation and adapter drift validation tasks.
- Does not apply to normal execution tasks.
- Includes runtime orchestration bootstrap exposure for main-session routing.

## Canonical Rule
- `.ai/agents/runtime/*.md` is the canonical source of runtime workers (16 files).
- `.ai/agents/personas/*.md` is the canonical source of stack/persona skill docs (6 files) and is NEVER an adapter source.
- Adapter generation must produce exactly one adapter per canonical runtime file, with matching filename (e.g. `backend-developer.md` -> `.claude/agents/backend-developer.md`).

## Canonical Runtime Role Set (16)

| Canonical Filename | Runtime Name | Mode | Description | Default Phase(s) |
|---|---|---|---|---|
| `project-manager.md` | `project-manager` | primary | Orchestrator. Owns delegation, classification, sequencing, and final assembly across all phases. | Planning Council, Implementation, QA, Business Go/No-Go, Commit/PR |
| `project-owner.md` | `project-owner` | subagent | Vision/scope owner. Provides discovery, business intent, and final risk acceptance. | Planning Council, Business Go/No-Go |
| `junior-project-manager.md` | `junior-project-manager` | subagent | Requirements-clarification assistant. Captures questions, drives user clarification loops. | Planning Council |
| `dev-team-lead.md` | `dev-team-lead` | subagent | Technical-approach owner. Defines architecture boundaries, contracts, and implementation handoff. | Planning Council, Implementation |
| `backend-developer.md` | `backend-developer` | subagent | Backend implementation worker for APIs, services, persistence, business logic. | Implementation |
| `frontend-developer.md` | `frontend-developer` | subagent | Frontend implementation worker for UI, state, integration, components. | Implementation |
| `database-administrator.md` | `database-administrator` | subagent | Schema/migration owner. Models data, plans migrations, owns rollback notes. | Implementation |
| `devops-engineer.md` | `devops-engineer` | subagent | Infrastructure, CI/CD, deployment, and release pipeline owner. | Implementation, Business Go/No-Go |
| `ui-ux-designer.md` | `ui-ux-designer` | subagent | UX flow, interaction, accessibility, and design-system owner. | Planning Council, Implementation |
| `web-designer.md` | `web-designer` | subagent | Visual/surface implementation for marketing pages and content surfaces. | Implementation |
| `cybersecurity-analyst.md` | `cybersecurity-analyst` | subagent | Threat modeling, security review, sensitive-change participation. | Planning Council, QA |
| `qa-specialist.md` | `qa-specialist` | subagent | Test execution: cases, fixtures, repros, coverage checks. | QA |
| `qa-team-lead.md` | `qa-team-lead` | subagent | QA consolidation and Quality Gate owner. | QA |
| `pr-manager.md` | `pr-manager` | subagent | Commit/PR handoff owner: commit message, PR body, change summary, doc-coordination. | Commit/PR |
| `documentation-writer.md` | `documentation-writer` | subagent | Doc author for feature docs, setup notes, changelog/readme updates. | Commit/PR |
| `doc-team-lead.md` | `doc-team-lead` | subagent | Documentation consolidation and doc-quality owner. | Commit/PR |

`project-manager` is the only `primary`. The other 15 are `subagent`.

## Persona Inheritance (Not Adapter Sources)

The following persona files at `.ai/agents/personas/*.md` are NEVER generated as runtime adapters. They are loaded on demand by matching runtime agents when task detection identifies the stack in the consumer project.

| Persona File | Inheriting Runtime Roles | Inheritance Trigger |
|---|---|---|
| `personas/laravel.md` | `backend-developer` | Laravel/PHP backend detected |
| `personas/fastapi.md` | `backend-developer` | FastAPI/Python web backend detected |
| `personas/node-express.md` | `backend-developer` | Node/Express backend detected |
| `personas/python.md` | `backend-developer` | Generic Python backend detected |
| `personas/react.md` | `frontend-developer`, `web-designer` | React frontend or React-rendered web surface detected |
| `personas/vue.md` | `frontend-developer`, `web-designer` | Vue frontend or Vue-rendered web surface detected |

Persona inheritance rules:
- `backend-developer` may inherit `laravel`, `fastapi`, `node-express`, or `python` based on the detected backend stack.
- `frontend-developer` may inherit `vue` or `react` based on detected frontend framework.
- `web-designer` may inherit `vue` or `react` when the marketing/web surface renders through a JS framework.
- Multiple personas may apply if the project spans multiple stacks; the runtime agent loads only the personas matched by its current task.
- Build workflows MUST NOT emit `.claude/agents/laravel.md`, `.codex/agents/vue.toml`, or any other adapter file derived from a persona file.

## Mapping Constraints
- Adapter names must match the canonical runtime filename exactly (kebab-case, `.md` stripped).
- Adapter names must be stable and unique within each runtime output directory.
- Mappings must include explicit source-role pointers to `.ai/agents/runtime/<name>.md`.
- Mappings must preserve policy compatibility and parent gate enforcement defined in `.ai/policies/approval-levels.md`.
- Mapping changes require drift-aware regeneration for affected adapters per `.ai/execution/adapter-drift-validation.md`.
- Parent/orchestrator runtime exposure rule: expose parent orchestration behavior through runtime bootstrap artifacts (`.ai/runtimes/<runtime>/orchestration-bootstrap.md` or equivalent), not through duplicated child adapters.

## Phase Mapping (5-Phase Flow)
- Planning Council: `project-owner`, `project-manager`, `dev-team-lead`, `ui-ux-designer` (when UI surface), `cybersecurity-analyst` (when sensitive). `junior-project-manager` participates for clarification.
- Implementation: `backend-developer`, `frontend-developer`, `database-administrator`, `devops-engineer`, `web-designer` as needed under `dev-team-lead` boundaries.
- QA: `qa-specialist` executes; `qa-team-lead` consolidates and owns the Quality Gate. `cybersecurity-analyst` participates for sensitive changes.
- Business Go/No-Go: `project-owner` (final risk acceptance), `project-manager` (release readiness), `devops-engineer` (deployment readiness when applicable).
- Commit/PR: `pr-manager` owns the handoff; coordinates `documentation-writer`/`doc-team-lead` for doc updates.

## Build Workflow Consumption
- `.ai/workflows/build-claude-agents.md` reads this file and emits exactly 16 adapters at `.claude/agents/<name>.md`.
- `.ai/workflows/build-codex-agents.md` reads this file and emits exactly 16 adapters at `.codex/agents/<name>.toml`.
- `.ai/workflows/build-opencode-agents.md` reads this file and emits exactly 16 adapters at `.opencode/agents/<name>.md`.

All three build workflows MUST refuse to generate any adapter whose name does not appear in the canonical role set above.
