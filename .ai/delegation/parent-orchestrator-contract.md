# Parent Orchestrator Contract

## Purpose
Define parent-agent responsibilities in delegated execution.

## Responsibilities
1. Load governing instructions:
   - `AGENTS.md`
   - `INDEX.md`
   - relevant `.ai/policies/*`
   - relevant `.ai/workflows/*`
   - `.ai/execution/*` and `.ai/delegation/*`
2. Reclassify every follow-up task with `.ai/execution/task-classification.md` before execution.
3. Decide mode using capability/eligibility contracts.
   - Do not require user to explicitly request delegated mode; choose automatically when classification + gate conditions match.
4. For Medium/Large tasks, complete planning outputs before implementation delegation:
   - `project-manager` (first)
   - `product-spec` (second)
   - consolidated spec/handoff artifact (parent-owned)
   - `architect` (after consolidated spec)
5. Select workflow and role mappings.
6. Assign disjoint ownership boundaries before spawning children.
7. Define expected artifact outputs and phase ownership before delegation (for example: `/artifacts/specs/YYYYMMDD-HHMMSS-final-spec.md`, `/artifacts/specs/YYYYMMDD-HHMMSS-payment-flow.mmd`, `/artifacts/architecture/YYYYMMDD-HHMMSS-checkout-sequence.mmd`).
8. Explicitly spawn/invoke children only when delegated mode is allowed.
9. Use targeted follow-up delegation when appropriate:
   - relevant implementation agents only,
   - `reviewer` when behavior changed,
   - `docs` when task classification marks docs required (for example docs/API/setup/decision/workflow changes),
   - parent final validation.
   - when `docs` is invoked, require `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md` before final validation (including remediation/final-rerun and non-merge-ready runs).
   - for review artifact-generating tasks, automatically route to `reviewer`; require `docs` when run report/audit/final report artifact is created.
   - include `tester` when validation, test interpretation, or coverage verification is requested.
10. Collect child outputs and integrate deterministically.
11. Enforce policy gates, quality checks, and final validation.
12. Produce final answer with clear execution disclosure:
   - whether delegation was used,
   - which child roles were used,
   - what merge/review steps were performed.
   - report path for `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md` when run is code-changing.
13. If any child raises a Requirement Clarification Gate (`.ai/policies/decision-gates.md`), pause downstream delegation, consolidate the blocking question, and ask the user before resuming.
14. Maintain explicit delegated workflow state:
   - `DISCOVERY`
   - `SPEC_DRAFT`
   - `SPEC_REVIEW`
   - `SPEC_APPROVED`
   - `ARCHITECTURE_READY`
   - `IMPLEMENTATION_ACTIVE`
   - `TESTING`
   - `REVIEW`
   - `DOCUMENTATION`
   - `COMPLETE`
15. Enforce code-changing auditability:
   - detect whether the run is code-changing (any repository file changed: source/tests/configs/docs/workflow contracts/generated artifacts),
   - ensure `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md` exists before completion,
   - for Tiny/Small efficiency cases where `docs` is not invoked, parent/main must write the run report and mark producer as `parent/main`.
   - if runtime trace export/update is requested, maintain it as a separate artifact from run report output.

## Spawn Idempotency Rule
- Parent must treat child spawn/invocation as idempotent per active role assignment.
- Before spawning a child, parent must check whether the same child role is already active for the same ownership scope.
- If same-role child is already active in that scope:
  - do not spawn a duplicate child,
  - route follow-up instructions to the active child, or
  - explicitly terminate/reassign before replacement.
- Parent must maintain an active-child registry for the duration of delegated execution and clear entries on child completion/failure.

## Display Label Rule
- When reporting spawned children, parent should format display labels as:
  - `<display-name> [<canonical-role>]`
- Example: `Marcus [frontend]`.
- Canonical role remains authoritative for routing/reporting; display label is readability metadata only.

## Follow-Up Planning-Agent Reuse Rule
Do not rerun `project-manager`/`product-spec`/`architect` for Tiny/Small follow-ups unless at least one applies:
- scope changes,
- acceptance criteria change,
- architecture changes,
- workflow changes significantly,
- user explicitly asks for re-planning.

Planning bypass rule for Tiny/Small:
- Do not require full PM/product-spec/architect pipeline by default.
- Use targeted delegation unless ambiguity, product behavior changes, architecture changes, multi-module impact, or risk escalation requires planning roles.

Review-only routing constraints:
- Pure review/analysis with no file changes may remain parent-only.
- Review artifact-generating tasks must not be parent-only when delegated capability is available.
- Do not require `backend`/`frontend`/`project-manager`/`product-spec`/`architect` for review-only tasks unless remediation/planning/architecture work is explicitly requested.

## Skip-Delegation Explanation Requirement
If parent handles a small multi-surface follow-up directly, parent must include:
- `Classification: Small multi-surface`
- `Delegation decision: skipped`
- `Reason: <low-risk | tightly coupled | faster parent patch | user did not request full delegation>`

## Parent Prohibitions
- Must not claim delegated execution when none occurred.
- Must not allow children to write outside assigned scopes without reassignment.
- Must not bypass policy/approval/quality gates.
- Must not skip mandatory planning gates for Medium/Large work.
- Must not spawn `backend` or `frontend` before both `SPEC_APPROVED` and `ARCHITECTURE_READY`.
- Must not complete a code-changing run without `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.

## Accountability
- Parent owns the final merged result.
- Parent owns unresolved conflict handling.
- Parent owns final risk disclosure.
- Parent consolidates `project-manager` + `product-spec` outputs into the approved spec handoff before invoking `architect`.
- Parent owns final merge and validation across implementation, testing, review, and docs outputs.
- Parent tracks artifact completeness by phase (`specs`, `architecture`, `tests`, `reviews`, `docs`) before marking workflow `COMPLETE`.
