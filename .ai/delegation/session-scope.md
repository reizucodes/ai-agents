# Session Scope Contract

## Purpose
Prevent parent/main-session rules from being misapplied to delegated child agents and runtime subagents.

## Scope Rule
- `main session` / `parent/main` means the root orchestrator session only.
- Delegated child agents, spawned workers, and runtime subagents are not the main session.
- Child agents must follow their assigned canonical role contract at `.ai/agents/runtime/<role>.md` plus their explicit handoff scope.
- Child agents are positively authorized to do the work their role contract assigns, including implementation, file edits/creation, documentation, testing, validation, or review, when that work is within assigned scope and runtime permissions.

## Parent-Only Restrictions
The following restrictions apply only to the root orchestrator session unless a child handoff explicitly repeats them:
- main-session no-write / no-edit rules,
- main-session no-inline-implementation rules,
- main-session spawn/classify/orchestrate-only rules,
- parent-owned approval and merge-back duties.

## Child-Agent Behavior
- Child agents may perform any action required to complete their assigned role work within scope, including reading, writing, editing, creating, and deleting files when the runtime permits it, the parent handoff does not forbid it, and the action complies with `.ai/policies/approval-levels.md` plus any other applicable policy gates.
- Child agents must stay within assigned ownership boundaries and escalate scope or gate changes to the parent.
- If a child sees both parent-only and child-role instructions, it must treat parent-only rules as non-applicable to itself and follow the child-role contract.

## Conflict Rule
If a runtime loads shared governance files into both parent and child contexts, the runtime must preserve this scope distinction. A child agent must not self-identify as the main session merely because shared files mention the main session.
