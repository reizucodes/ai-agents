# Execution Capability Gates

## Purpose
Define hard capability checks required before specialist delegation or approved fallback. The preflight output template lives in `.ai/execution/artifact-conventions.md`.

## Gate Categories

### Gate 0: Role-Specific Runtime Worker Discovery
- Determine `required_roles` from classification, scope, risk, runtime metadata.
- Check that a matching adapter exists per runtime:
  - Claude: `.claude/agents/<role>.md`
  - Codex: `.codex/agents/<role>.toml`
  - OpenCode: matching native worker / config entry
- Record `available_adapters` and `missing_adapters` by canonical runtime name.
- Missing required worker → disclose, request approval before fallback, never claim delegation.

### Gate 1: Runtime Capability
All must be true:
- Runtime can spawn subagents explicitly.
- Runtime can return child outputs to parent.
- Runtime can preserve child context isolation.
- Runtime can enforce bounded concurrency.

Unknown → treat as not supported; disclose; request approval before fallback.

### Gate 2: Invocation Integrity
- Parent chooses execution mode from classification + eligibility.
- Parent explicitly spawns children.
- Parent does not claim delegation if no children were spawned.

### Gate 3: Governance Compatibility
- Required approvals remain enforceable (see `.ai/policies/approval-levels.md`).
- Quality and definition-of-done gates remain enforceable.
- Security/risk policy application remains intact.

### Gate 4: Merge Control
- Parent can assign disjoint file ownership.
- Parent can integrate child outputs deterministically.
- Parent can resolve conflicts and preserve review traceability.

### Gate 5: Planning Gate Compliance (Medium/Major)
Before implementation delegation, all must be true:
- Planning phase output exists (`project-manager` / Planning Council).
- Spec output exists (`feature-spec.md` or PRD equivalent).
- Technical design output exists (`technical-design.md` or equivalent).
- Required proposal artifacts exist as repository files (see artifact-conventions.md).
- Parent presented consolidated proposal package to user.
- Explicit user approval is recorded.

Failure → stop and resolve planning/artifact/approval gaps. Do not start implementation roles.

## Capability Decision
`targeted` and `delegated` modes are allowed only when all relevant gates and role-specific adapter checks pass.

## Non-claims
- Presence of `.ai/agents/*` does not imply runtime delegation capability.
- Presence of delegation contracts does not imply runtime delegation capability.
- Runtime support must be confirmed at execution time.
