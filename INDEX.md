# AI Agents Quick Start

This library is risk-scaled: use the lightest process that still protects quality and safety.

## Runtime Entry
Preferred initialization order:
1. `AGENTS.md`
2. `INDEX.md`
3. `.ai/context/project-intelligence.md` (if present)
4. Other relevant `.ai/context/*` files
5. `.ai/execution/*` and `.ai/delegation/*` (execution contracts, excluding adapter-only files)
6. `.ai/agents/runtime/*` (canonical runtime role contracts)
7. Relevant policies, then the relevant workflow
8. Target codebase inspection as needed

`.ai/agents/personas/*` (`laravel`, `fastapi`, `node-express`, `python`, `vue`, `react`) are NOT loaded at startup. They are inherited on demand by `backend-developer`, `frontend-developer`, or `web-designer` when the task targets that stack.

Adapter-contract routing rule (skip on normal startup, load only when explicitly building/validating adapters):
- `.ai/execution/runtime-adapter-contract.md`
- `.ai/execution/adapter-role-mapping.md`
- `.ai/execution/adapter-drift-validation.md`
- `.ai/runtimes/codex/adapter-schema.md`
- `.ai/runtimes/codex/nickname-strategy.md`
- `.ai/runtimes/claude/adapter-schema.md`
- `.ai/runtimes/claude/tool-mapping.md`
- `.ai/runtimes/opencode/adapter-schema.md`
- `.ai/runtimes/opencode/agent-mapping.md`
- `.ai/runtimes/opencode/workflow-mapping.md`
- `.ai/runtimes/opencode/command-mapping.md`
- `.ai/runtimes/opencode/delegation.md`
- `.ai/runtimes/opencode/validation.md`
- `.ai/workflows/build-codex-agents.md`
- `.ai/workflows/build-claude-agents.md`
- `.ai/workflows/build-opencode-agents.md`

Bootstrap files to load when present:
- Codex main session: `.ai/runtimes/codex/orchestration-bootstrap.md`
- Claude main session: `.ai/runtimes/claude/orchestration-bootstrap.md`
- OpenCode: `opencode.json` and `.ai/runtimes/opencode/bootstrap.md`

Startup guidance:
- For runtimes that do not auto-load `AGENTS.md` (Claude Code, Cursor, Cline, Roo Code), explicitly prompt the runtime to read `AGENTS.md` and `INDEX.md` before execution.
- The root (primary) agent is the sole spawner; a leaf subagent never spawns. PM owns planning/coordination; the other 15 roles run as subagents. Per-runtime spawning split: see `AGENTS.md` § Orchestration Baseline.
- When a suitable specialist exists, delegation is required by default; the user does not need to ask for it.
- If runtime subagent capability or required workers are unavailable, disclose the limitation and obtain explicit approval before sequential role simulation fallback, unless the user explicitly says `no subagent` or `main only`.
- Execution mode is runtime metadata only — it never determines delegation or auto-spawns agents.
- Markdown instruction files do not spawn agents by themselves; the orchestrator must classify, check capability, then explicitly invoke child agents.

## Execution Contracts
Loaded on normal startup:
- `.ai/execution/modes.md`
- `.ai/execution/capability-gates.md`
- `.ai/execution/task-classification.md` (absorbs delegation eligibility and confidence-gate rule)
- `.ai/execution/artifact-conventions.md` (Artifact Requirement Matrix)
- `.ai/execution/delegation-routing-regression.md`
- `.ai/delegation/parent-orchestrator-contract.md`
- `.ai/delegation/child-role-contracts.md`
- `.ai/delegation/ownership-boundaries.md`
- `.ai/delegation/merge-review-validation.md`

Rules:
- `.ai/agents/runtime/*` is the canonical role contract source.
- Delegated execution is never implied by file presence alone.
- Parent orchestrator owns ownership boundaries, merge, and final validation when delegation is used.

## Canonical Runtime Roles
Sixteen named workers, all under `.ai/agents/runtime/`:

`backend-developer`, `cybersecurity-analyst`, `database-administrator`, `dev-team-lead`, `devops-engineer`, `doc-team-lead`, `documentation-writer`, `frontend-developer`, `junior-project-manager`, `pr-manager`, `project-manager`, `project-owner`, `qa-specialist`, `qa-team-lead`, `ui-ux-designer`, `web-designer`.

`project-manager` runs as `primary`; the other 15 run as `subagent`. Stack knowledge (`laravel`, `fastapi`, `node-express`, `python`, `vue`, `react`) lives in `.ai/agents/personas/*` and is inherited on demand by `backend-developer`, `frontend-developer`, or `web-designer` — never spawned as its own worker.

## Quick Start Matrix
Artifact requirements come from `.ai/execution/artifact-conventions.md`.

| Size | Examples | Participating Roles | Required Artifacts | Gates |
|---|---|---|---|---|
| Tiny | Typo, copy tweak, CSS nudge | `project-manager`, one implementer (`backend-developer` / `frontend-developer` / `web-designer`) | None beyond conversation output | Auto |
| Small | Pagination, simple endpoint, minor bug fix | `project-manager`, `dev-team-lead`, one implementer, `qa-specialist` | None required | Auto + Recommendation if risky |
| Medium | New module, cross-domain feature, sizeable API change | `project-manager`, `project-owner`, `dev-team-lead`, implementer(s) (`backend-developer`/`frontend-developer`/`database-administrator`), `qa-specialist`, `qa-team-lead`, `pr-manager` | `feature-spec.md`, run-report | Recommendation; Approval at Business Go/No-Go |
| Major | New subsystem, breaking change, major refactor | Full Planning Council + Implementation + QA + Business Go/No-Go + `pr-manager`; add `ui-ux-designer` for UI surface | `feature-spec.md` + `technical-design.md` + run-report + proposal review package | Approval at Business Go/No-Go |
| Security / Deploy | Authn/authz, payments, sensitive data, public API, file upload, schema change, release | Above + `cybersecurity-analyst` (security) and/or `devops-engineer` (deploy); `database-administrator` for schema | `threat-model.md` (security), `technical-design.md` + migration/rollback notes (schema), `release.md` workflow + run-report (deploy), PR description from `pr-manager` | Approval at Business Go/No-Go; secrets-management, quality-gates enforced |

## Recommended Flows
All workflows follow the same 5-phase flow; phases collapse for lighter work.

### Tiny
Planning Council collapses to `project-manager` -> implementer commits -> `pr-manager` summary.

### Small
Planning Council (`project-manager` + `dev-team-lead`) -> implementer -> `qa-specialist` -> `pr-manager`.

### Medium
Planning Council (`project-owner`, `project-manager`, `dev-team-lead`, `junior-project-manager` if requirements need clarification) -> Implementation under `dev-team-lead` -> `qa-specialist` + `qa-team-lead` -> Business Go/No-Go (`project-owner` + `project-manager`) -> `pr-manager` Commit/PR (with `documentation-writer` for docs).

### Major
Full Planning Council (add `ui-ux-designer` for UI surface, `cybersecurity-analyst` if sensitive) -> Implementation -> QA -> Business Go/No-Go with proposal review package and explicit Approval -> `pr-manager` + `doc-team-lead`/`documentation-writer`.

### Security / Deployment
Full Major flow with mandatory `cybersecurity-analyst` (security) and `devops-engineer` (deploy); use `release.md` for shipping work.

Workflow entry points:
- `bugfix.md` for defects/regressions.
- `feature.md` for net-new behavior.
- `refactor.md` for internal structural improvement without intended behavior change.
- `release.md` for deployment/release readiness.

## Agent Participation Guidance

### Planning Council
`project-owner` defines vision and scope; `project-manager` owns orchestration and gate enforcement; `dev-team-lead` proposes technical approach; `junior-project-manager` runs requirements clarification when scope is fuzzy; `ui-ux-designer` joins when a UI surface is affected; `cybersecurity-analyst` joins for sensitive work. Output: scoped plan plus artifact requirements driven by risk class.

### Implementation
`backend-developer`, `frontend-developer`, `database-administrator`, `devops-engineer`, and `web-designer` execute under `dev-team-lead` boundaries. Stack work pulls in `.ai/agents/personas/*` as inherited context, not as a separate worker.

### QA
`qa-specialist` executes test plans and validation; `qa-team-lead` consolidates results and owns the Quality Gate. Recommended on most work; skip only for trivial non-functional edits.

### Business Go/No-Go
`project-owner` makes the final risk-acceptance call; `project-manager` confirms release readiness. Single explicit Approval gate — documented decision required for Major / security / deployment / release-impacting work.

### Commit / PR
`pr-manager` owns the commit message, PR body, and change summary; `documentation-writer` and `doc-team-lead` produce or update docs when changes affect public surface or operations.

## Template Selection
- `task.md` for quick daily work.
- `feature-spec.md` for Medium+ or ambiguous work.
- `prd.md` for Medium/Large product specification.
- `technical-design.md` for Medium/Large technical design and all schema changes.
- `threat-model.md` for security-sensitive changes.
- `adr.md` when decisions alter architecture/contracts.
- `proposal-review-package.md` for Business Go/No-Go approval at Major / security / deploy work.
- `adapter-run-report.md` for runtime adapter generation/validation reporting (default paths: Codex `.ai/reports/codex-adapter-run-report.md`; Claude `.ai/reports/claude-adapter-run-report.md`).

## Practical Rule
Start small, escalate only when risk or complexity increases.

## Runtime Routing Metadata
Preferred: keep the user prompt natural; let runtime/launcher metadata carry routing when available.

Fallback only when runtime metadata cannot be provided: add `Execution mode: <auto|sequential|targeted|delegated>` in the prompt body.

## Classification Rule
- Classify work first using `.ai/execution/task-classification.md`.
- Reclassify every follow-up task before execution.
- Tiny/Small work avoids unnecessary planning overhead.
- Medium+ work follows the full 5-phase flow with required artifacts before implementation begins.

## Workflow Automation Model
Prompt
-> Planning Council
-> Implementation
-> QA
-> Business Go/No-Go
-> Commit/PR

- Stage outputs feed the next stage automatically; the orchestrator advances without prompting unless a gate fires.
- Auto and Recommendation gates resolve without halting the run.
- The **Approval** gate at Business Go/No-Go halts execution for Major / security-sensitive / deployment-impacting work. `project-manager` must present a proposal review package using `.ai/templates/proposal-review-package.md` and wait for explicit user approval before implementation continues to release.

## Main Session Responsibilities
- Read `AGENTS.md` and `INDEX.md`.
- Classify the task and select the workflow.
- Treat execution mode as runtime routing metadata only.
- Assign agents, ownership boundaries, and required artifacts.
- Validate agent outputs against the Artifact Requirement Matrix.
- Consolidate planning outputs into a proposal review package when the Business Go/No-Go Approval gate applies.
- Integrate final results and report assumptions, skipped items, changed files, validation results, and remaining risks.

Main orchestrator must not:
- skip approval gates or assume approval,
- silently continue past the Business Go/No-Go gate,
- implement directly when a matching runtime role exists unless the user explicitly requested `no subagent`/`main only` or approved a disclosed fallback,
- collapse specialist roles into itself,
- claim delegation when only sequential simulation occurred.

## Policy Map
- Approval boundaries and gate types (Auto / Recommendation / Approval): `.ai/policies/approval-levels.md`
- Risk classification (drives the Artifact Requirement Matrix): `.ai/policies/risk-classification.md`
- Quality gates: `.ai/policies/quality-gates.md`
- Completion baseline: `.ai/policies/definition-of-done.md`
- Secrets handling: `.ai/policies/secrets-management.md`

## Project Knowledge Base
`.ai/context/*` is a host-project knowledge layer that reduces repeated codebase discovery, preserves architectural understanding, and accelerates onboarding.

If `.ai/context/` exists, consult it before architecture, implementation, refactoring, testing, security review, or release work. Knowledge files are accelerators, not absolute truth — current target codebase source remains the source of truth, and conflicts resolve toward target evidence (then update intelligence files).

If `.ai/context/confidence-gates.md` exists, use it before implementation to classify the change and determine required pre-implementation checks. Confidence gates matter most for authn/authz, sensitive data, public APIs, data-model changes, external integrations, background jobs, configuration/environment changes, deployment changes, and security-sensitive work.

Refresh knowledge artifacts after architectural changes, major dependency changes, integration changes, domain-model changes, large refactors, or release-process changes.

Example structure:

```txt
.ai/context/
├── project-intelligence.md
├── architecture.md
├── domain-model.md
├── coding-patterns.md
├── integrations.md
├── testing-strategy.md
├── technical-debt.md
└── confidence-gates.md
```

## Examples
Use `examples/*/README.md` for scenario references that show collaboration patterns by stack, not complete generated implementation artifacts.

