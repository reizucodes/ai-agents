# Merge, Review, and Validation Contract

## Purpose
Define deterministic integration and final quality control for delegated runs.

## Merge Order
Recommended order:
1. Contract-sensitive changes (API/schema/interfaces).
2. Core implementation changes.
3. Test additions/updates.
4. Review-driven remediations.

Parent may adjust order if dependencies require, but must preserve traceability.

## Conflict Handling
- Parent detects overlapping edits.
- Parent resolves conflicts; children do not self-resolve across boundaries unless reassigned.
- Parent documents conflict decisions and residual risk.

## Review Requirements
- Apply relevant `.ai/policies/*` and definition-of-done criteria.
- Ensure no unresolved high-severity defects/findings remain.
- Verify delegated claims match actual execution.
- Detect and flag orchestration violations:
  - Approval Gate Violation,
  - Artifact Contract Violation,
  - Delegation Contract Violation,
  - Orchestrator Role Violation,
  - Premature Implementation Handoff.
- Verify:
  - planning halted before implementation,
  - required proposal artifacts were generated as files,
  - parent/orchestrator consolidated planning outputs,
  - user approval was requested,
  - user approval was explicit,
  - implementation started only after explicit approval,
  - targeted/delegated used subagents when supported,
  - scoped specialist outputs/reports are present for delegated specialists when subagents were used,
  - main agent remained orchestrator.

## Validation Requirements
- Validate acceptance criteria against selected workflow.
- Validate security/approval requirements for risk class.
- Validate test evidence and known gaps.

## Final Output Requirements
Parent must state:
- execution mode used,
- child roles invoked (if delegated),
- merge strategy applied,
- validation status,
- unresolved risks or follow-ups.
