# Task Classification Contract

## Purpose
Classify requested work before selecting planning depth, execution mode, and delegation strategy.

## Follow-Up Reclassification Rule
Every follow-up task must be reclassified before execution.

- Follow-up tasks inherit current feature context.
- Follow-up tasks do not inherit previous delegation decisions.
- Delegation must be selected from current follow-up classification and risk.

## Classification Criteria
Evaluate all dimensions before classifying:
- Scope breadth: files/modules/surfaces touched.
- Coupling: cross-domain dependencies and contract impact.
- Risk: security, auth, payment, public API, migration, release impact.
- Ambiguity: requirement clarity and decision uncertainty.
- Coordination load: number of roles needed to produce safe output.

Code-changing run rule:
- A run is code-changing when it modifies any repository file (source, tests, configs, docs, workflow contracts, or generated artifacts).
- Code-changing runs require an audit report artifact at `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- Non-code-changing runs (pure Q&A/explanation/search/planning-only with no file changes) do not require an audit report artifact.

Requirement ambiguity rule:
- when blocking ambiguity exists, trigger the Requirement Clarification Gate in `.ai/policies/decision-gates.md` before classification-dependent execution continues.

Review-only task types:
- Pure review / analysis only:
  - No repository file changes.
  - Parent/main allowed.
  - `reviewer` optional.
  - No audit artifact required.
- Review artifact-generating:
  - Creates/updates review/report/audit/final-report artifacts.
  - Requires targeted delegation to `reviewer`.
  - Requires `docs` when run report/audit/final report artifact is created.
  - Requires `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- Review + validation:
  - Review task that also verifies test results, coverage, or validation status.
  - Requires `reviewer`.
  - `tester` is required when test interpretation/coverage/validation verification is requested.
  - `docs` required when artifacts change.
- Review + remediation:
  - Review task that includes requested fixes/remediation work.
  - Route to remediation flow and relevant implementation agents.
  - Rerun `tester` -> `reviewer` -> `docs`.

## Levels

### Tiny
Examples:
- typo fixes
- variable renames
- small validation fixes
- simple docs corrections

Default flow:
- Parent
- Implementation
- Review

Default behavior:
- Sequential mode.
- Parent may handle directly.
- Skip planning agents (`project-manager`, `product-spec`, `architect`).
- Use targeted delegation to relevant implementation role(s) when files are changed; parent-only is allowed only for non-code-changing tasks.
- `docs` may be skipped for efficiency, but code-changing Tiny runs still require `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md` (written by `docs` when invoked, otherwise by parent/main).
- No diagrams required.

### Small
Examples:
- single endpoint
- small isolated UI change
- export button
- simple enhancement

Default flow:
- Parent
- Lightweight design note
- Implementation
- Review
- Docs (optional)

Default behavior:
- Sequential mode.
- `architect` only when contract/boundary risk appears.
- Relevant implementation role(s) are required for code-changing work via targeted delegation.
- `docs` is optional for efficiency, but code-changing Small runs still require `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md` (written by `docs` when invoked, otherwise by parent/main).
- Diagrams optional.

### Small Follow-Up Thresholds

#### Small single-surface follow-up
Examples:
- backend-only CORS fix
- frontend-only copy/UI tweak
- docs-only summary

Default:
- for code-changing runs, use targeted delegation to the relevant implementation role (`backend` for backend-only, `frontend`/framework specialist for frontend-only, `docs` for docs-only, `tester` for test-only).
- parent may handle directly only for non-code-changing runs.

#### Small multi-surface follow-up
Examples:
- backend endpoint + frontend view
- API change + tests
- UI change + docs

Default:
- parent must consider targeted subagents.
- code-changing runs must use targeted delegation to relevant implementation roles (for example `backend` + `frontend` for cross-layer changes).

Required skip-delegation explanation:
- `Classification: Small multi-surface`
- `Delegation decision: skipped`
- `Reason: <low-risk | tightly coupled | faster parent patch | user did not request full delegation>`

### Medium
Examples:
- feature spanning backend/frontend/tests
- notification preferences
- profile management
- payment retry flow

Default flow:
- Parent
- Project Manager
- Product Spec
- Architect
- Backend + Frontend + Tester
- Reviewer
- Docs
- Parent

Default behavior:
- Spec-first planning gates are expected.
- Backend/frontend/tester may run in parallel only after planning outputs are complete.
- `docs` is required when feature behavior, setup, API, workflow, or decisions changed.
- `docs` creates `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- Diagrams are encouraged.

### Large
Examples:
- new product module
- payment platform integration
- subscription system
- service decomposition
- high-risk architecture change

Default flow:
- Parent
- Project Manager
- Product Spec or PRD
- Architect or Technical Design
- Backend + Frontend + Tester
- Reviewer
- Docs
- Parent Final Validation

Default behavior:
- Spec-first planning gates are mandatory.
- Delegation may be used after planning artifacts are approved.
- `docs` is required when feature behavior, setup, API, workflow, or decisions changed.
- `docs` creates `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- Diagrams are expected when workflows, boundaries, ownership, or API flows are non-trivial.

## Escalation Rules
Escalate classification upward when any apply:
- new/changed external contract or public API,
- auth/authz/sensitive data/payment surface,
- cross-domain or multi-team impact,
- migration/rollback complexity,
- unclear requirements blocking implementation.
- review-only task expands into remediation implementation across code surfaces.

Clarification precedence:
- unresolved requirement ambiguity must pass the Requirement Clarification Gate before continuing with classification-driven planning or implementation decisions.

## Downgrade Rules
Downgrade only when evidence confirms reduced scope:
- requirement and risk surfaces are narrowed,
- cross-domain dependencies removed,
- no meaningful contract/security/ops impact remains.

## Planning-Agent Skip Rules
`project-manager` and `product-spec` may be skipped only for Tiny/Small when:
- requirements are explicit,
- acceptance criteria are already testable,
- no medium+ risk trigger exists.

`architect` may be skipped only when:
- no boundary, data contract, or architecture decision is required.

`docs` may be skipped only when:
- task is Tiny and not explicitly requested, or
- task is Small with no behavior/setup/API/workflow/decision changes.
- When skipped in a code-changing Tiny/Small run, parent/main must still create `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.

## Planning-Agent Rerun Rules for Follow-Ups
Do not rerun `project-manager`/`product-spec`/`architect` for Tiny/Small follow-ups unless at least one applies:
- scope changes,
- acceptance criteria change,
- architecture changes,
- workflow changes significantly,
- user explicitly asks for re-planning.

## Spec-First Gate Rules
Spec-first gates are mandatory for Medium/Large:
1. Planning output exists (`project-manager` scope/handoff).
2. Product specification/PRD exists.
3. Technical design decision exists (`architect` or technical-design artifact).

Implementation agents must not begin Medium/Large execution before these gates pass.
