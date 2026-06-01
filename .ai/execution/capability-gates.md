# Execution Capability Gates

## Purpose
Define hard capability checks required before using delegated execution.

## Gate Categories

### Gate 0: Runtime Adapter Discovery (Codex)
For Codex runtime task routing that expects generated adapters:
- Check whether `.codex/agents/*.toml` exists.
- Record discovery as `present` or `missing`.

If missing for a run that requests/needs `targeted` or `delegated` adapter-based routing:
- Disclose the limitation explicitly.
- Request user approval before sequential role simulation fallback.
- Do not claim delegation occurred.

### Gate 1: Runtime Capability
All must be true:
- Runtime can spawn subagents/children explicitly.
- Runtime can return child outputs to parent.
- Runtime can enforce or preserve child context isolation.
- Runtime can run with bounded concurrency controls.

If any is unknown:
- Treat as not supported.
- Disclose the limitation explicitly.
- Request user approval before sequential role simulation fallback.
- Do not claim delegation occurred.

### Gate 2: Invocation Integrity
All must be true:
- Parent chooses execution mode from classification + eligibility (no user "delegated mode" request required).
- Parent explicitly requests/spawns child agents.
- Parent does not claim delegation if no child agents were spawned.

If false:
- Disclose the limitation explicitly.
- Request user approval before sequential role simulation fallback.
- Do not claim delegation occurred.

### Gate 3: Governance Compatibility
All must be true:
- Required approvals remain enforceable.
- Quality and definition-of-done gates remain enforceable.
- Security/risk policy application remains intact.

If false:
- Disclose the limitation explicitly.
- Request user approval before sequential role simulation fallback.
- Do not claim delegation occurred.

### Gate 4: Merge Control
All must be true:
- Parent can assign disjoint file ownership.
- Parent can integrate child outputs deterministically.
- Parent can resolve conflicts and preserve review traceability.

If false:
- Disclose the limitation explicitly.
- Request user approval before sequential role simulation fallback.
- Do not claim delegation occurred.

### Gate 5: Planning Gate Compliance
For Medium/Large classification, all must be true before implementation delegation:
- Planning phase output exists (`project-manager`).
- Product specification output exists (`product-spec` or PRD equivalent).
- Technical design output exists (`architect` or technical design equivalent).
- Required proposal artifacts exist as repository files.
- Parent/orchestrator has presented consolidated proposal package to user.
- Explicit user approval for implementation is recorded.

If false:
- Stop and resolve planning/artifact/approval gaps first.
- Do not start implementation roles (`backend`, `frontend`, or other code-writing roles).

## Capability Decision
`targeted` and `delegated` modes are allowed only when all required gates pass.
When an explicit execution-mode input is provided, enforce `.ai/execution/execution-mode-input.md`.

## Non-claims
- Presence of `.ai/agents/*` does not imply runtime delegation capability.
- Presence of delegation contracts does not imply runtime delegation capability.
- Runtime support must be confirmed at execution time.
