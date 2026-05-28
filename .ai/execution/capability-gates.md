# Execution Capability Gates

## Purpose
Define hard capability checks required before using delegated execution.

## Gate Categories

### Gate 1: Runtime Capability
All must be true:
- Runtime can spawn subagents/children explicitly.
- Runtime can return child outputs to parent.
- Runtime can enforce or preserve child context isolation.
- Runtime can run with bounded concurrency controls.

If any is unknown:
- Treat as not supported.
- Use sequential mode.

### Gate 2: Invocation Integrity
All must be true:
- Parent chooses execution mode from classification + eligibility (no user "delegated mode" request required).
- Parent explicitly requests/spawns child agents.
- Parent does not claim delegation if no child agents were spawned.

If false:
- Use sequential mode.

### Gate 3: Governance Compatibility
All must be true:
- Required approvals remain enforceable.
- Quality and definition-of-done gates remain enforceable.
- Security/risk policy application remains intact.

If false:
- Use sequential mode.

### Gate 4: Merge Control
All must be true:
- Parent can assign disjoint file ownership.
- Parent can integrate child outputs deterministically.
- Parent can resolve conflicts and preserve review traceability.

If false:
- Use sequential mode.

### Gate 5: Planning Gate Compliance
For Medium/Large classification, all must be true before implementation delegation:
- Planning phase output exists (`project-manager`).
- Product specification output exists (`product-spec` or PRD equivalent).
- Technical design output exists (`architect` or technical design equivalent).

If false:
- Use sequential planning completion first.
- Do not start delegated implementation roles.

## Capability Decision
Delegated mode is allowed only when all required gates pass.

## Non-claims
- Presence of `.ai/agents/*` does not imply runtime delegation capability.
- Presence of delegation contracts does not imply runtime delegation capability.
- Runtime support must be confirmed at execution time.
