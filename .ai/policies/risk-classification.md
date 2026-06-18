# Risk Classification Policy

## Purpose
Provide consistent impact-based risk language across planning, implementation, QA, review, and release decisions. Risk tier drives required artifacts; see the artifact requirement matrix in `.ai/execution/artifact-conventions.md`.

## Levels

## Low
- **Description:** Minor localized change with low blast radius.
- **Examples:** UI copy fix, non-critical refactor, doc-only API clarification.
- **Required Reviews:** Self-review by the implementing role; `qa-specialist` spot checks.
- **Required Approvals:** Auto (L0) actions only unless state-changing operations occur.
- **Workflow Implications:** Standard gates, lighter regression scope.

## Medium
- **Description:** Moderate functional impact or shared module change.
- **Examples:** New endpoint in existing service, state management changes, migration with simple rollback.
- **Required Reviews:** `qa-specialist` + `pr-manager`.
- **Required Approvals:** Recommendation (L1) for dependency/migration/state-changing operations.
- **Workflow Implications:** Full Architecture/Implementation/Quality gates.

## High
- **Description:** Significant business/system impact, sensitive data/auth or multi-service coupling.
- **Examples:** Auth model changes, cross-service contract changes, complex schema migrations.
- **Required Reviews:** `dev-team-lead` + `qa-team-lead` + `pr-manager` + `devops-engineer` readiness input.
- **Required Approvals:** Recommendation (L1) actions must be explicitly approved; Approval (L2) prohibited without explicit user command.
- **Workflow Implications:** `cybersecurity-analyst` participation; security-focused QA and release gate mandatory.

## Critical
- **Description:** Potential severe outage, data loss, security breach, or production instability.
- **Examples:** Production data migration with irreversible steps, auth credential handling changes, critical infrastructure operations.
- **Required Reviews:** `dev-team-lead` + `qa-team-lead` + `pr-manager` + `devops-engineer` + `cybersecurity-analyst`, with documented escalation.
- **Required Approvals:** Approval (L2) explicit user command required for execution-sensitive actions.
- **Workflow Implications:** Incident-level controls; release blocked until all mitigations are verified.

## Risk → Artifact Mapping
Risk tier drives which row of the artifact requirement matrix applies (see `.ai/execution/artifact-conventions.md`). In summary:
- Low → matches the "Tiny / Small (non-sensitive)" row by default; escalate when scope expands.
- Medium → matches the "Medium feature" row.
- High → matches "Major feature / major fix / major refactor" or "Security-sensitive" as applicable.
- Critical → matches "Security-sensitive" + "Deployment-impacting" rows together.

## Escalation Rules
1. Escalate to `dev-team-lead` when domain boundaries or contract compatibility are uncertain.
2. Escalate to `devops-engineer` for runtime/deployment risk at High or Critical levels.
3. Escalate to `pr-manager` for unresolved review concerns; escalate to `cybersecurity-analyst` for security concerns.
4. Reclassify risk if new evidence increases blast radius.
