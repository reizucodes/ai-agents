# Quality Gates Policy

## Purpose
Prevent unsafe workflow progression with mandatory gate validation for the gates selected by workflow and risk path.

Workflow files determine gate depth/applicability by risk level (low/medium/high). Once a gate is selected, its requirements are mandatory.
For low-risk work, evidence may be lightweight (for example: focused checks, targeted tests, concise validation notes) while still meeting Definition of Done and applicable safety policies.

## Architecture Gate
### Requirements
- Feature specification exists.
- Acceptance criteria defined.
- Risks identified.
- Architecture documented.
- API contracts defined when applicable.

### Failure Behavior
Stop progression and request missing inputs before implementation starts.

### Example
A new endpoint is proposed without response/error contract. Gate fails until contract is defined.

## Proposal Gate
### Requirements
- Planning outputs are complete for required planning roles.
- Required proposal artifacts exist as repository files.
- Parent/orchestrator consolidated planning outputs into a proposal review package.
- User explicitly approved implementation.

### Failure Behavior
Stop progression and block implementation handoff until artifacts, consolidation, and explicit approval are complete.

### Example
Planning notes exist in chat but required proposal files are missing. Proposal gate fails.

## Implementation Gate
### Requirements
- Feature implementation complete for agreed scope.
- Tests added for changed behavior.
- Documentation updated.
- No compilation/build errors.
- No failing required tests.

### Failure Behavior
Return implementation deficiencies and block QA handoff until resolved.

### Example
Feature compiles locally but integration tests fail on validation edge case. Gate fails.

## Quality Gate
### Requirements
- `qa-specialist` review complete; `qa-team-lead` consolidates results.
- `cybersecurity-analyst` review complete when risk is Medium+ or auth/data boundaries changed.
- Critical defects resolved.
- Acceptance criteria satisfied.

### Failure Behavior
Block merge/release progression.

### Example
QA finds authorization bypass (high severity). Gate remains blocked until fix + retest.

## Release Gate
### Requirements
- Documentation complete (`documentation-writer` / `doc-team-lead`).
- Required tests passing.
- PR review completed by `pr-manager`.
- Deployment plan documented by `devops-engineer`.
- Rollback plan documented and validated.

### Failure Behavior
Block release readiness status.

### Example
No rollback for schema migration is documented. Release gate fails.
