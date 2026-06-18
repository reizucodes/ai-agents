# Bugfix Workflow

## Purpose
Resolve defects through the canonical 5-phase flow with a lean default. QA always runs; Planning Council and Business Go/No-Go collapse unless the root cause crosses sensitive surfaces.

## Scope Sizing
- **Tiny** fix (typo, isolated one-liner): Phase 1 = `project-manager` only (or skipped if root cause is obvious); Phase 4 auto.
- **Small** fix (single module, no contract change): collapsed Planning Council; Phase 4 auto.
- **Medium** fix (multi-module, behavior change, regression risk): full collapsed council + conditionals; Phase 4 logged decision.
- **Major** fix (security incident, data corruption, cross-service contract): full Planning Council with conditionals; Phase 4 explicit Approval.

Classify per `.ai/execution/task-classification.md`. Reclassify after root cause is known.

## Phase 1: Planning Council
**Default (collapsed) members:** `project-manager` + `dev-team-lead`.

**Expand when root cause touches:**
- Schema or data model → add `database-administrator`.
- Auth, sensitive data, payments, attack surface → add `cybersecurity-analyst` and `project-owner`.
- Deployment, runtime config, infra → add `devops-engineer`.
- User-facing change beyond defect restoration → add `ui-ux-designer`.

**Outputs:**
- Reproduction steps, root-cause hypothesis (or known root cause), blast radius, risk tier per `.ai/policies/risk-classification.md`.
- Fix strategy + regression scope.
- Artifact requirements per matrix below.

**Gate:** Auto for Tiny/Small; Recommendation (L1) for Medium when tradeoffs exist; Approval (L2) for Major.

Cross-reference: `.ai/agents/runtime/project-manager.md`, `dev-team-lead.md`, `database-administrator.md`, `cybersecurity-analyst.md`, `devops-engineer.md`.

## Phase 2: Implementation
**Lead:** `dev-team-lead` for Medium/Major; targeted single role for Tiny/Small.
**Executors (scoped by root cause):**
- `backend-developer`, `frontend-developer`, `database-administrator`, `devops-engineer`, `web-designer` — only those whose surface contains the defect.

Prefer minimal, reversible changes. State-changing ops follow `.ai/policies/approval-levels.md`. Secrets follow `.ai/policies/secrets-management.md`.

## Phase 3: QA (Always Runs)
- `qa-specialist` reproduces the original defect, verifies the fix, runs regression suite scoped to the affected surface and adjacent contracts.
- `qa-team-lead` consolidates results and owns the **Quality Gate** per `.ai/policies/quality-gates.md`.
- Definition of Done per `.ai/policies/definition-of-done.md`.

A bugfix is not done until the failing scenario has an automated regression test (or a documented reason why one is not feasible).

## Phase 4: Business Go/No-Go
**Members:** `project-owner` + `project-manager` (project-owner may be skipped for non-regression Small/Medium).

**Gate type:**
- Non-regression Tiny / Small / Medium: **Auto** pass-through; decision logged in run report.
- Regression to user-visible behavior, or Medium fix with residual risk: **Recommendation** (L1).
- Major (security, data, cross-service): **Approval** (L2) — explicit user approval required.

## Phase 5: Commit / PR
- `pr-manager` owns commit message (link defect/ticket), PR body, change summary, regression-test references, merge handoff.
- `documentation-writer` updates changelog / release notes / operational runbook where impacted; `doc-team-lead` reviews.
- Run report at `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md` for all code-changing runs per `.ai/execution/artifact-conventions.md`.

Cross-reference: `.ai/agents/runtime/pr-manager.md`, `documentation-writer.md`, `doc-team-lead.md`, `qa-specialist.md`, `qa-team-lead.md`.

## Artifact Requirements by Risk Tier
Per `.ai/execution/artifact-conventions.md` requirement matrix.

| Tier | Required artifacts |
|---|---|
| Tiny / Small (non-sensitive) | none beyond conversation outputs and PR description |
| Medium | run-report + brief root-cause note in PR body |
| Major | `feature-spec.md` (incident scope) + `technical-design.md` (fix design) + run-report + proposal review package |
| Schema-touching | add `technical-design.md` + migration/rollback notes |
| Security-sensitive | add `threat-model.md` + proposal review package |
| Deployment-impacting | add `release.md` workflow handoff + run-report |

## Decision Gates Summary
- **Auto (L0):** non-regression Tiny/Small/Medium fixes; reproductions; read-only diagnosis.
- **Recommendation (L1):** Medium fix tradeoffs; user-visible regression remediation; dependency bumps as part of fix.
- **Approval (L2):** Major (security/data/infra); hotfix to production; destructive cleanup ops.

## Exit Criteria
- Defect reproduced before fix; fix verified.
- Regression test added (or skip documented).
- Quality Gate passed; no new high/critical defects.
- Required artifacts present.
- Phase 4 decision recorded.
- PR opened by `pr-manager` linking the defect.
