# Execution Mode Input Contract

## Purpose
Define runtime-facing routing metadata with capability-aware fallback behavior.

Preferred UX:
- Execution mode is runtime metadata selected by launcher/session context, not required inside every user task prompt body.
- Execution mode does not determine whether delegation is required.
- The main session is not an implementation agent.

## Supported Modes
- `auto`:
  - default routing metadata when no explicit value is provided.
  - parent classifies task and routes to the required specialist path.
- `sequential`:
  - explicit parent-only bypass.
  - use only when the user explicitly says `no subagent` or `main only`, or when fallback was disclosed and explicitly approved.
- `targeted`:
  - orchestrator remains parent/main and delegates selected specialist tasks when runtime subagents are supported.
  - use only relevant agents for the classified scope.
  - do not silently absorb specialist work into parent/main.
  - enforce proposal gates and proposal artifact contracts when planning is in scope.
  - examples: `frontend`/`vue`, `backend`/`fastapi`, `reviewer`/`documentation`, `tester` only.
- `delegated`:
  - full orchestration mode.
  - parent/main is strictly orchestrator and must not implement directly.
  - required subagent spawning based on workflow contracts.
  - roles must not be collapsed into parent/main.
  - enforce proposal gates and proposal artifact contracts.

## Preferred Input Sources
Use execution mode from runtime metadata in this priority order:
1. Launcher-selected value.
2. Runtime/session header/context value injected before task execution.
3. Prompt-body header fallback.

If no value is provided by runtime metadata, default to `auto`.

Runtime metadata/header examples:

```md
Execution mode (runtime): auto|sequential|targeted|delegated
```

```md
Runtime capability: <available|unavailable|unknown>
Codex adapters: <present|missing|unknown>
```

## Prompt-Body Fallback (Allowed, Not Preferred)
Fallback only when runtime metadata/header injection is unavailable:

```md
Execution mode: <auto|sequential|targeted|delegated>
```

Prompt-body input is routing input only. It does not automatically spawn agents, does not bypass classification, and does not override capability, adapter, planning, approval, or safety gates. Parent/main must still classify the task, run role-specific preflight, and explicitly spawn/invoke child agents only after gates pass.

## Capability-Aware Preconditions
Before `targeted` or `delegated` execution, parent/main must check:
1. Runtime delegation capability (from `.ai/execution/capability-gates.md`).
2. Exact required role adapter availability (`.codex/agents/<role>.toml`) when running in Codex adapter mode.
3. Planning/proposal approval state when the classified task is Medium/Large.

If either required precondition is unavailable:
- report the limitation explicitly,
- request user approval before sequential role simulation fallback,
- do not claim delegation occurred.

## Routing Notes
- `auto` should prefer delegation only when capability + eligibility + mapping availability are satisfied.
- matching specialist work still requires delegation by default when the required worker path is available.
- `targeted` should select minimal relevant agent set.
- `delegated` should follow full Medium/Large or required review/remediation flow.
- `delegated` is not direct implementation and not sequential role simulation.
- Tiny/Small targeted `backend`, `frontend`, and `tester` runs do not require `SPEC_APPROVED` or `ARCHITECTURE_READY` unless classification escalates due to risk, scope, ambiguity, contract changes, architecture changes, or user-requested planning.
- For Medium/Large planning in `targeted` or `delegated`, implementation roles must not start before proposal artifacts are validated, the proposal package is presented using `.ai/templates/proposal-review-package.md`, and explicit approval is received.
- This contract is runtime-facing and does not replace canonical role/flow contracts.
