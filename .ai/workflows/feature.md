# Feature Workflow

## Purpose
Deliver new features with clear contracts, predictable quality, and merge readiness.

## Participating Agents
`project-manager`, `product-spec`, `architect`, stack agent (`laravel`/`vue`/`react`/`node-express`/`fastapi`/`python`), `tester`, `reviewer`, optionally `security`, `devops`.

Participation scales by risk path below; not all agents are used for every change.

## Execution Order
Use the risk paths below to choose the right depth. The sequence below reflects the structured flow used for medium/high-risk work.

Implementation planning ownership:
- `project-manager` owns planning coordination, phase state, and proposal handoff readiness for Medium/High-risk work.
- `product-spec` owns goals, user stories, acceptance criteria, and implementation-ready scope for Medium/High-risk work.
- `architect` owns technical implementation planning for non-trivial/boundary-impacting work.
- stack agent owns stack-specific implementation steps and execution details.

0. If the goal is vague, early-stage, or not implementation-ready, run `ideation` first to refine direction before product specification.
1. Project-manager coordinates scope, milestones, phase state, and handoff readiness.
2. Product-spec defines goals, user stories, and acceptance criteria.
3. Architect defines boundaries, contracts, risks.
4. Security reviews design when risk/surface warrants it.
5. Stack agent implements per contract and coding standards after proposal approval.
6. QA defines and executes risk-based test plan.
7. Code-review validates correctness and maintainability.
8. DevOps validates release readiness for high-impact changes.

## Stage Progression Rules
- Stages should continue automatically whenever no Decision Gate is triggered.
- Stage outputs automatically become inputs to the next stage.
- Do not pause only because a stage completed.
- Only Recommendation Decisions and Approval Decisions may pause execution.
- Approval Decisions must follow `.ai/policies/approval-levels.md`.
- Decision Gate behavior must follow `.ai/policies/decision-gates.md`.
- For planning-gated feature flows, Proposal Approval Gate is mandatory before implementation:
  - parent/orchestrator must halt after planning,
  - required proposal artifacts must exist as repository files,
  - consolidated proposal package must be presented using `.ai/templates/proposal-review-package.md`,
  - explicit user approval is required before code-writing agents run.

## Deliverables
- Feature spec
- Implementation plan and decisions
- Tests and results summary
- Review findings and resolution log

## Success Criteria
- Acceptance criteria met.
- Required tests passing.
- No unresolved high-severity review findings.
- Contract docs updated.

## Escalation Rules
- Contract ambiguity -> `architect`.
- Reproducible quality failures -> stack agent + `tester`.
- Release blockers -> `devops`.

## Recommended Usage
Use for all net-new user-facing features and API capabilities.

## Low Risk Path
- **Templates:** `task.md` (required), `feature-spec.md` optional.
- **Required Agents:** stack agent.
- **Recommended Agents:** `tester`.
- **Optional Agents:** `reviewer`.
- **Gates:** Implementation + Quality (lightweight evidence).
- **Policies:** risk-classification, definition-of-done, runtime-safety/approval-levels when applicable.

## Medium Risk Path
- **Templates:** `feature-spec.md` (required), `adr.md` optional.
- **Required Agents:** `project-manager`, `product-spec`, `architect`, stack agent, `tester`, `reviewer`.
- **Optional Agents:** `security`.
- **Gates:** Architecture + Implementation + Quality.
- **Policies:** risk-classification, quality-gates, definition-of-done, approval-levels/runtime-safety.

## High Risk Path
- **Templates:** `feature-spec.md` + `threat-model.md` (recommended), `adr.md` optional.
- **Required Agents:** `project-manager`, `product-spec`, `architect`, `security`, stack agent, `tester`, `reviewer`.
- **DevOps:** required when deployment/ops behavior changes.
- **Gates:** Architecture + Implementation + Quality + Release (for production readiness).
- **Policies:** all governance policies, especially secrets-management.

## Agent Handoffs
- Project-manager -> Product-spec: scope, milestones, assumptions, planning state, and handoff readiness.
- Product-Spec -> Architect: business goals, user stories, acceptance criteria, constraints.
- Architect -> Stack: domain boundaries, API/data contracts, risk list.
- Architect -> Security (conditional): threat-relevant design context and risk class.
- Stack -> QA: implemented behavior, changed surfaces, known limitations.
- Security -> QA/Code-review (conditional): prioritized security findings and validation targets.
- QA -> Code-review: failing scenarios, residual risk.
- Code-review -> Stack: required changes before approval.

## Gate Selection
Gate requirements vary by risk level.

- **Low Risk:** lightweight validation, Definition of Done, and applicable safety policies.
- **Medium Risk:** Implementation Gate + Quality Gate (and Architecture Gate when boundary/contract decisions are introduced).
- **High Risk:** Architecture + Implementation + Quality gates, plus Release Gate when production readiness is affected.

See Low/Medium/High risk paths above.

## Risk Assessment
- Must be performed at architecture stage and revisited after implementation.
- Use `.ai/policies/risk-classification.md`.
- Include `security` review when any of the following apply:
  - authentication or authorization exists,
  - sensitive data handling exists,
  - public APIs exist,
  - file uploads exist,
  - payment functionality exists,
  - risk classification is Medium or higher.

## Approval Requirements
- Apply `.ai/policies/approval-levels.md` and `.ai/policies/runtime-safety.md`.
- Level 1 actions require approval; Level 2 actions require explicit user command.

## Handoff Requirements
- Project-manager -> Product-spec: planning scope, milestones, constraints, blockers, and proposal-readiness status.
- Product-Spec -> Architect: scoped requirements, acceptance criteria, assumptions, dependencies.
- Architect -> Stack: spec link, ADR, risk class, required gates.
- Architect -> Security (conditional): threat surfaces, trust boundaries, data classification.
- Stack -> QA: changed modules, tests added, known risks, DoD status.
- Security -> QA/Code-review (conditional): findings with severity and remediation guidance.
- QA -> Code-review: defect list (severity/priority), gate status.
- Code-review -> DevOps/Release: approval state, residual risk, required release controls.

## Exit Criteria
- All required gates passed.
- Definition of Done satisfied.
- No unresolved high/critical defects or findings.
