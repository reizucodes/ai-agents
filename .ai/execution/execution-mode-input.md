# Execution Mode Input Contract

## Purpose
Define a runtime-facing execution-mode input model with capability-aware fallback behavior.

## Supported Modes
- `auto`:
  - default mode.
  - parent classifies task and selects best mode.
  - may escalate to `targeted` or `delegated` when capability checks pass.
- `sequential`:
  - force parent/main-only execution.
  - no subagent spawning unless hard escalation is required by policy/gates.
- `targeted`:
  - use only relevant agents for the classified scope.
  - examples: `frontend`/`vue`, `backend`/`fastapi`, `reviewer`/`docs`, `tester` only.
- `delegated`:
  - full orchestration mode.
  - required subagent spawning based on workflow contracts.

## Input Header Convention
Use a lightweight header before execution:

```md
Execution mode: <auto|sequential|targeted|delegated>
```

Optional runtime context header:

```md
Runtime capability: <available|unavailable|unknown>
Codex adapters: <present|missing|unknown>
```

## Capability-Aware Preconditions
Before `targeted` or `delegated` execution, parent/main must check:
1. Runtime delegation capability (from `.ai/execution/capability-gates.md`).
2. Generated Codex adapters availability (`.codex/agents/*.toml`) when running in Codex adapter mode.

If either required precondition is unavailable:
- fall back to `sequential`,
- state fallback reason explicitly in output.

## Routing Notes
- `auto` should prefer delegation only when capability + eligibility + mapping availability are satisfied.
- `targeted` should select minimal relevant agent set.
- `delegated` should follow full Medium/Large or required review/remediation flow.
- This contract is runtime-facing and does not replace canonical role/flow contracts.
