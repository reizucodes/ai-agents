# Refactor Workflow

## Purpose
Improve internal structure with no functional change, through the canonical 5-phase flow. `dev-team-lead` leads Planning Council; QA validates behavior parity.

## Scope Sizing
- **Tiny** refactor (rename, extract local helper): Phase 1 = `dev-team-lead` only; Phase 4 auto.
- **Small** refactor (single module reorganization): collapsed council; Phase 4 auto.
- **Medium** refactor (cross-module, internal API shape): full Planning Council with conditionals; Phase 4 logged decision.
- **Major** refactor (architecture change, public API/contract change, service decomposition): full Planning Council with proposal; Phase 4 explicit Approval.

Classify per `.ai/execution/task-classification.md`. If a refactor reveals behavior change is required, escalate to feature or bugfix workflow.

## Phase 1: Planning Council
**Lead:** `dev-team-lead` (technical-direction owner).
**Required members:** `dev-team-lead` + `project-manager`.
**Conditional members:**
- `project-owner` — refactor changes public API/contract, deprecates a feature, or has user-visible impact.
- `database-administrator` — schema/data-model refactor.
- `devops-engineer` — deployment topology, runtime, pipeline refactor.
- `cybersecurity-analyst` — refactor touches auth, sensitive data, secrets handling.
- `ui-ux-designer` — refactor changes visible UI structure even when intent is non-functional.

**Outputs:**
- Refactor goal + non-goals (explicit: "no behavior change").
- Behavior parity definition (which tests/contracts must remain green).
- Risk tier per `.ai/policies/risk-classification.md`.
- Migration steps for in-flight consumers if internal API surface shifts.
- Artifact requirements per matrix.

**Gate:** Auto for Tiny/Small; Recommendation (L1) for Medium; Approval (L2) for Major or public-API/contract changes.

Cross-reference: `.ai/agents/runtime/dev-team-lead.md`, `project-manager.md`, `project-owner.md`, `database-administrator.md`, `devops-engineer.md`, `cybersecurity-analyst.md`.

## Phase 2: Implementation
**Lead:** `dev-team-lead`.
**Executors (scoped by surface):** `backend-developer`, `frontend-developer`, `database-administrator`, `devops-engineer`, `web-designer`.

Rules:
- Prefer mechanical, reviewable steps (extract → move → rename → inline).
- Keep refactor commits separate from any incidental fixes.
- No new tests of new behavior; existing tests must remain green.

State-changing ops follow `.ai/policies/approval-levels.md`.

## Phase 3: QA (Behavior Parity)
- `qa-specialist` runs the full impacted regression suite, contract tests, and any characterization tests captured pre-refactor.
- For Major refactors: capture baseline behavior fingerprints (snapshot tests, golden output, contract recordings) before Phase 2 starts.
- `qa-team-lead` owns the **Quality Gate** per `.ai/policies/quality-gates.md`. Gate passes only when behavior is provably unchanged.
- Definition of Done per `.ai/policies/definition-of-done.md`.

Any behavior delta found in QA reverts the change to Phase 2 or escalates to feature/bugfix workflow.

## Phase 4: Business Go/No-Go
**Members:** `project-manager` (always) + `project-owner` (when public API/contract changes).

**Gate type:**
- Internal-only refactor (no public surface change): **Auto** pass-through; decision logged in run report.
- Internal API change with internal consumers: **Recommendation** (L1).
- Public API / external contract change: **Approval** (L2) — explicit user approval required.

## Phase 5: Commit / PR
- `pr-manager` owns commit message (explicit "refactor, no behavior change"), PR body framing parity evidence, change summary, merge handoff.
- `documentation-writer` updates internal architecture notes, ADRs, and any API references that move; `doc-team-lead` reviews.
- Run report at `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md` per `.ai/execution/artifact-conventions.md`.

Cross-reference: `.ai/agents/runtime/pr-manager.md`, `documentation-writer.md`, `doc-team-lead.md`, `qa-specialist.md`, `qa-team-lead.md`.

## Artifact Requirements by Risk Tier
Per `.ai/execution/artifact-conventions.md` requirement matrix.

| Tier | Required artifacts |
|---|---|
| Tiny / Small (non-sensitive) | none beyond conversation outputs and PR description |
| Medium | run-report + short design note in PR body |
| Major | `technical-design.md` + run-report + proposal review package |
| Schema-touching | add `technical-design.md` + migration/rollback notes |
| Security-sensitive | add `threat-model.md` + proposal review package |
| Deployment-impacting | add `release.md` workflow handoff + run-report |

## Decision Gates Summary
- **Auto (L0):** internal-only refactors; mechanical rename/extract; non-destructive moves.
- **Recommendation (L1):** internal API reshaping with multiple consumer paths; dependency restructures.
- **Approval (L2):** public API or external contract change; schema redesign; runtime/infra topology refactor.

## Exit Criteria
- Behavior parity demonstrated (regression suite + characterization evidence as scoped).
- No new failing tests; no new behavior introduced.
- Quality Gate passed.
- Required artifacts present.
- Phase 4 decision recorded.
- PR opened by `pr-manager` framed as refactor with parity evidence.
