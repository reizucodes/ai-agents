# Task Classification Contract

## Purpose
Classify work before selecting planning depth, delegation strategy, and artifact requirements. See `.ai/policies/risk-classification.md` for risk tiers and `.ai/execution/artifact-conventions.md` for the artifact requirement matrix.

## Follow-Up Reclassification Rule
Every follow-up task is reclassified before execution.
- Follow-ups inherit feature context, not previous delegation decisions.
- Delegation is decided from current classification + risk.

## Classification Criteria
Evaluate all dimensions:
- **Scope breadth** — files/modules/surfaces touched.
- **Coupling** — cross-domain dependencies, contract impact.
- **Risk** — security, auth, payment, public API, migration, release impact (see risk-classification.md).
- **Ambiguity** — requirement clarity, decision uncertainty.
- **Coordination load** — number of roles needed.

Code-changing run: any run that modifies repository files. Code-changing runs require a run report (`/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`) per artifact-conventions.md.

## Size / Scope Levels

| Level | Examples | Default Flow |
|---|---|---|
| **Tiny** | typo, rename, one-line fix, doc correction | Implementation only; skip planning roles |
| **Small** | single endpoint, isolated UI tweak, simple enhancement | Lightweight design note + implementation + review |
| **Medium** | feature spanning backend/frontend/tests, profile mgmt, payment retry | Planning Council + implementation + QA + docs |
| **Major** | new product module, payment platform, subscription system, service decomposition, high-risk architecture change | Full 5-phase flow with spec + technical-design + proposal approval gate |

## Confidence Gate (Single Rule)

If **any** of the following apply, require an explicit pre-implementation confirmation from the user:
- Risk ≥ **Medium** (per risk-classification.md), OR
- Ambiguity is **High** (requirements unclear, multiple valid interpretations), OR
- Scope touches **authentication, authorization, schema or data model, public API, payments, or deployment/infrastructure**.

Otherwise, proceed without a confidence gate.

The confirmation may be a single targeted message ("Proceed with approach X for Y? Y/N") and may run inside the same conversation turn. For Major work it escalates to the full proposal approval gate.

## Delegation Decision Matrix

When to delegate vs. main-only.

| Situation | Decision |
|---|---|
| Non-code-changing Q&A / search / explanation | Main-only allowed |
| Pure review/analysis, no artifact output | Main-only allowed; `pr-manager` optional |
| Code-changing Tiny single-surface | Targeted delegation to one relevant role (`backend-developer`, `frontend-developer`, etc.) |
| Code-changing Small single-surface | Targeted delegation to one role |
| Code-changing Small multi-surface | Targeted delegation to disjoint roles (e.g. `backend-developer` + `frontend-developer`) |
| Review artifact-generating (audit, report, final-report) | Targeted delegation to `pr-manager` + `documentation-writer` (and `qa-specialist` when validation in scope) |
| Medium feature | Full delegated flow after Planning Council |
| Major feature / refactor | Full delegated flow with proposal approval gate before implementation |
| Required worker unavailable | Disclose gap, request approval before sequential simulation; honor `no subagent` / `main only` |

Rejection rules (full delegated mode):
- Tiny/Small single-surface with no parallelism benefit.
- Heavily coupled changes likely to conflict.
- Auditability needs a single deterministic trace.

## Escalation Rules
Escalate classification upward when any apply:
- New/changed external contract or public API.
- Auth/authz/sensitive data/payment surface.
- Cross-domain or multi-team impact.
- Migration/rollback complexity.
- Unclear requirements blocking implementation (triggers confidence gate).
- Review-only task expands into remediation.

## Downgrade Rules
Downgrade only when evidence confirms reduced scope: narrowed requirements, removed cross-domain deps, no contract/security/ops impact.

## Planning-Role Skip Rules
- `project-manager` and `project-owner` may be skipped for Tiny/Small when requirements are explicit and risk is Low.
- `dev-team-lead` may be skipped when no boundary, data contract, or architecture decision is required.
- `documentation-writer` may be skipped for Tiny work; code-changing runs still produce the run report (written by parent/main if no doc role is invoked).

## Planning Reuse Rule (Follow-Ups)
Do not rerun `project-manager` / `project-owner` / `dev-team-lead` for Tiny/Small follow-ups unless scope, acceptance criteria, architecture, or workflow changes significantly, or the user explicitly requests re-planning.
