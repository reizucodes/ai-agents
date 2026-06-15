# Release Workflow

## Purpose
Ship a versioned release to production through the canonical 5-phase flow. Planning Council is owner-led; Implementation is ops-led; QA runs full regression; Business Go/No-Go is always an explicit Approval gate; PR phase produces release notes.

## Scope Sizing
- **Patch** (low-risk fix bundle, no schema/infra change): full 5 phases, lean artifact set.
- **Minor** (new features, additive changes, possible schema): full 5 phases, full artifact set.
- **Major** (breaking changes, irreversible migrations, infra cutover): full 5 phases, full artifact set + rollback rehearsal evidence.

Tiny "no-op" releases (e.g. docs-only re-tag) may collapse Phase 2 to a `devops-engineer` cut-and-tag step; Phases 1/3/4/5 still run.

## Phase 1: Planning Council
**Required members:** `project-owner` + `project-manager` + `devops-engineer` + `cybersecurity-analyst`.
**Conditional members:**
- `dev-team-lead` — release includes contract changes, multi-service coupling, or risk reclassification.
- `database-administrator` — release includes schema migration or data backfill.
- `qa-team-lead` — present from Phase 1 for regression scoping when scope is non-trivial.

**Outputs:**
- Release scope: included changes, excluded changes, compatibility statement, customer-impact summary.
- Risk tier per `.ai/policies/risk-classification.md` (release tier overrides individual change tiers).
- Deployment strategy + rollback plan (commands, data, observability checkpoints).
- Regression scope, performance/security validation scope.
- Communications plan (release notes audience, downtime/SLA messaging).
- Artifact requirements per matrix below.

**Gate:** Approval (L2) — every release requires explicit owner sign-off on scope before Phase 2.

Cross-reference: `.ai/agents/runtime/project-owner.md`, `project-manager.md`, `devops-engineer.md`, `cybersecurity-analyst.md`, `dev-team-lead.md`, `database-administrator.md`, `qa-team-lead.md`.

## Phase 2: Implementation
**Lead:** `devops-engineer` for release execution; `qa-team-lead` for validation orchestration.
**Executors:**
- `devops-engineer` — release branch, build, artifact promotion, staged deploys, infra/runtime changes, secret rotation handling per `.ai/policies/secrets-management.md`.
- `qa-team-lead` — schedules regression, sets gates for staged environments.
- `database-administrator` (conditional) — migration execution with rollback rehearsal in pre-prod.

Production-impacting operations follow Approval (L2) per `.ai/policies/approval-levels.md`. No production deploy proceeds without explicit user command.

## Phase 3: QA (Full Regression)
- `qa-specialist` executes: full regression suite, smoke tests in staged environments, performance/load checks where in scope, observability/alert verification, rollback rehearsal where required.
- `cybersecurity-analyst` validates security-sensitive surfaces touched by the release.
- `qa-team-lead` consolidates results across environments and owns the **Quality Gate** per `.ai/policies/quality-gates.md`. Quality Gate must pass in pre-prod before Phase 4.
- Definition of Done per `.ai/policies/definition-of-done.md`.

## Phase 4: Business Go/No-Go (Explicit Approval)
**Members:** `project-owner` (final risk acceptance) + `project-manager` (release readiness) + `devops-engineer` (operational readiness) + `cybersecurity-analyst` (security clearance, when in scope).

**Gate type:** **Approval (L2) — always.** Release never enters production without explicit user approval recorded against:
- regression evidence,
- rollback readiness,
- known residual risks,
- on-call coverage and observability,
- secrets/credential handling status.

A No-Go returns to Phase 2 (fix) or aborts the release window.

## Phase 5: Commit / PR (Release Notes + Tagging)
- `pr-manager` owns: release branch merge PR, tag creation request (executed by `devops-engineer` under L2), commit hygiene, change manifest.
- `documentation-writer` produces release notes (customer-facing + internal changelog); `doc-team-lead` reviews for accuracy and completeness.
- `devops-engineer` records deploy outcome, post-release verification, and incident-readiness handoff.
- Run report at `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md` per `.ai/execution/artifact-conventions.md`.

Cross-reference: `.ai/agents/runtime/pr-manager.md`, `documentation-writer.md`, `doc-team-lead.md`, `devops-engineer.md`, `qa-specialist.md`, `qa-team-lead.md`.

## Artifact Requirements by Release Tier
Per `.ai/execution/artifact-conventions.md` requirement matrix. Every release is treated as deployment-impacting and PR/release handoff.

| Tier | Required artifacts |
|---|---|
| Patch | release-notes + run-report + rollback plan in PR body |
| Minor | `feature-spec.md` (release scope) + release-notes + run-report + rollback plan |
| Major | `feature-spec.md` + `technical-design.md` + `threat-model.md` (when security-touching) + release-notes + run-report + proposal review package + rollback rehearsal evidence |
| Schema-touching | add migration/rollback notes referenced by the release plan |
| Security-sensitive | add `threat-model.md` + cybersecurity-analyst sign-off in Phase 4 |

## Decision Gates Summary
- **Auto (L0):** read-only validation, building artifacts, dry-run deploys to disposable envs.
- **Recommendation (L1):** staged deploy to pre-prod, migration in shared non-prod, dependency promotion.
- **Approval (L2):** production deploy, production migration, secret rotation, tag creation, rollback execution, infra destruction. Phase 4 is always L2.

## Exit Criteria
- Quality Gate passed in pre-prod and verified in production smoke.
- Phase 4 explicit approval recorded.
- Deploy completed without unresolved critical incident; rollback path verified and remains available.
- Observability/alerts active; on-call informed.
- Release notes published; PR merged; tag created.
- Run report committed.
