# PR Review Template

## Summary
- Change overview:
- Scope boundaries:
- High-level risk:

## Architecture Review
- Design consistency with existing boundaries:
- Coupling/cohesion impact:
- Tradeoffs accepted/rejected:

## Testing Review
- Unit coverage:
- Integration/feature coverage:
- Regression coverage:
- Missing cases:

## Security Review
- Input validation:
- Authn/Authz:
- Sensitive data handling:
- Dependency/security concerns:

## Performance Review
- Query and I/O impact:
- Runtime/memory considerations:
- Caching strategy:

## Documentation Review
- API docs updated:
- Runbooks/migrations noted:
- Developer notes sufficient:

## Risk Assessment
- High risks:
- Medium risks:
- Low risks:

## Approval Checklist
- [ ] Requirements satisfied
- [ ] Tests pass and are meaningful
- [ ] Security concerns addressed
- [ ] Performance risks acceptable
- [ ] Documentation complete
- [ ] Ready to merge

## Orchestration Compliance Checklist
- [ ] Planning halted before implementation (when planning roles were used)
- [ ] Required proposal artifacts exist as repository files
- [ ] Parent/orchestrator consolidated planning outputs into a proposal package
- [ ] User was asked to review and approve proposal package
- [ ] Approval was explicit (not inferred from silence/non-objection)
- [ ] Implementation started only after explicit approval
- [ ] Targeted/delegated mode used subagents when runtime supported
- [ ] Main agent remained orchestrator
- [ ] No simulated role output mislabeled as delegated child output
- [ ] No parent direct implementation occurred in delegated mode

## Required Violation Labels
- Approval Gate Violation
- Artifact Contract Violation
- Delegation Contract Violation
- Orchestrator Role Violation
- Premature Implementation Handoff
