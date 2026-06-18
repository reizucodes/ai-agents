# Feature Workflow

## Purpose
Deliver new features through the canonical 5-phase flow: Planning Council → Implementation → QA → Business Go/No-Go → Commit/PR.

## Scope Sizing
- **Tiny** feature (one-line/copy/flag toggle): collapse Phase 1 to `project-manager` only; skip Phase 4 (auto pass-through).
- **Small** feature (single surface): Planning Council = `project-manager` + `dev-team-lead`; Phase 4 auto pass-through.
- **Medium** feature: full Planning Council; explicit Recommendation gate on tradeoffs; Phase 4 logged decision.
- **Major** feature: full Planning Council with proposal artifacts; explicit Approval gate at Phase 4.

Classify per `.ai/execution/task-classification.md` before entering Phase 1.

## Phase 1: Planning Council
**Members (required):** `project-owner`, `project-manager`, `dev-team-lead`.
**Conditional members:**
- `ui-ux-designer` — feature touches a user-facing surface.
- `cybersecurity-analyst` — feature touches auth, sensitive data, payments, or external attack surface.
- `database-administrator` — feature requires schema or data-model change.
- `devops-engineer` — feature changes deployment, infra, runtime config, or release surface.

**Outputs:**
- Scoped plan: goals, user stories, acceptance criteria, in/out of scope.
- Risk tier per `.ai/policies/risk-classification.md`.
- Artifact requirement list (see matrix below).
- Implementation participant list for Phase 2.

**Gate:** Recommendation (L1) for Medium; Approval (L2) for Major (proposal review package per `.ai/policies/approval-levels.md`).

Cross-reference role contracts: `.ai/agents/runtime/project-owner.md`, `project-manager.md`, `dev-team-lead.md`, `ui-ux-designer.md`, `cybersecurity-analyst.md`, `database-administrator.md`, `devops-engineer.md`.

## Phase 2: Implementation
**Lead:** `dev-team-lead` enforces boundaries and integration points.
**Executors (as scoped by Planning Council):**
- `backend-developer` — server logic, APIs, domain.
- `frontend-developer` — client behavior, state, integration.
- `database-administrator` — migrations, indexes, data contracts.
- `devops-engineer` — pipeline, infra, runtime config.
- `web-designer` — visual/markup implementation when distinct from frontend logic.

State-changing operations follow Recommendation (L1) / Approval (L2) gates per `.ai/policies/approval-levels.md`. Secrets handling follows `.ai/policies/secrets-management.md`.

## Phase 3: QA
- `qa-specialist` executes the risk-based test plan: success paths, validation failures, edge cases, regressions.
- `qa-team-lead` consolidates results and owns the **Quality Gate** per `.ai/policies/quality-gates.md`.
- Definition of Done verified per `.ai/policies/definition-of-done.md`.

Quality Gate must pass before Phase 4. Reproducible failures return to Phase 2.

## Phase 4: Business Go/No-Go
**Members:** `project-owner` (final risk acceptance) + `project-manager` (release readiness).

**Gate type:**
- Tiny / Small: **Auto** pass-through; decision logged in run report.
- Medium: **Recommendation** — present residual risk + readiness summary; default to ship.
- Major: **Approval** — explicit user approval required before Phase 5.

## Phase 5: Commit / PR
- `pr-manager` owns: commit message, PR body, change summary, diff hygiene, merge handoff.
- `documentation-writer` updates user/API/operational docs; `doc-team-lead` reviews for coverage and consistency.
- For code-changing runs: run report at `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md` per `.ai/execution/artifact-conventions.md`.

Cross-reference role contracts: `.ai/agents/runtime/pr-manager.md`, `documentation-writer.md`, `doc-team-lead.md`, `qa-specialist.md`, `qa-team-lead.md`.

## Artifact Requirements by Risk Tier
Per `.ai/execution/artifact-conventions.md` requirement matrix.

| Tier | Required artifacts |
|---|---|
| Tiny / Small (non-sensitive) | none beyond conversation outputs and PR description |
| Medium | `feature-spec.md` + run-report |
| Major | `feature-spec.md` + `technical-design.md` + run-report + proposal review package |
| Schema-touching | add `technical-design.md` + migration/rollback notes |
| Security-sensitive | add `threat-model.md` + proposal review package |
| Deployment-impacting | add `release.md` workflow handoff + run-report |

## Decision Gates Summary
- **Auto (L0):** Tiny/Small implementation steps; non-destructive ops; planning summaries.
- **Recommendation (L1):** Medium tradeoff choices; dependency/migration in shared envs; Phase 4 for Medium.
- **Approval (L2):** Major Phase 1 proposal; production deploy; destructive ops; Phase 4 for Major.

## Exit Criteria
- Acceptance criteria met.
- Quality Gate passed; no unresolved high/critical defects.
- Required artifacts present in repository.
- Phase 4 decision recorded.
- PR opened by `pr-manager` with docs updated.
