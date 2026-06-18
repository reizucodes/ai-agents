# Execution Modes Contract

## Purpose
Define runtime routing shapes. See `.ai/execution/task-classification.md` for classification, `.ai/policies/approval-levels.md` for gates, and `.ai/execution/capability-gates.md` for capability preconditions.

## Core Rules
- This instruction framework is not a runtime; markdown files do not spawn agents.
- The main session is not an implementation agent. When a suitable specialist exists, delegation is required.
- Execution mode is runtime-facing routing metadata, not the primary control surface.
- Every code-changing run persists `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.

## Execution Mode Input
Accepted values: `auto` (default), `sequential`, `targeted`, `delegated`.

Input priority:
1. Launcher / runtime metadata.
2. Runtime/session header injected before execution.
3. Prompt-body `Execution mode: ...` (fallback only).

Prompt-body input is routing input only; it does not spawn agents, does not override classification, and does not bypass capability/approval gates. Parent/main still classifies and runs preflight before invoking children.

Capability preconditions before `targeted` / `delegated`: see `.ai/execution/capability-gates.md`. If unavailable, disclose the limitation and request approval before any sequential simulation fallback. Never claim delegation that did not occur.

## Modes

### Mode A: Sequential
Parent/main executes serially in one runtime agent.

- **When**: user explicitly says `no subagent` / `main only`; runtime has no reliable subagent support and the user approves disclosed fallback; or task is pure non-code-changing analysis.
- **Who participates**: parent/main only.
- **Notes**: all policy/quality gates still apply. Code-changing runs in Mode A require explicit `no subagent` / `main only` from the user — parent/main MUST NOT implement inline as a silent fallback or under any self-determined justification.

### Mode B: Planning-Gated Delegated
Full orchestration mode for Medium/Major work.

- **When**: runtime supports subagents; classification is Medium/Major; planning gates apply.
- **Who participates**: parent/main as orchestrator; Planning Council (`project-owner`, `project-manager`, `dev-team-lead`, optionally `ui-ux-designer` / `cybersecurity-analyst`); implementation roles (`backend-developer`, `frontend-developer`, `database-administrator`, `devops-engineer`, `web-designer` as needed); `qa-specialist` + `qa-team-lead`; `pr-manager`; `documentation-writer` / `doc-team-lead`.
- **Notes**: parent stays strictly orchestrator. Implementation does not begin until Planning Council outputs are approved (see proposal approval gate in approval-levels.md).

### Mode C: Targeted Delegation
Spawn only the minimal relevant specialist(s).

- **When**: classified work needs one or more specialists; full Medium/Major planning is unnecessary or already complete; runtime subagents + role adapters available.
- **Who participates**: parent/main as orchestrator + the targeted specialist(s) only.
- **Notes**: parent does not silently collapse specialist work into itself. Tiny/Small targeted runs do not require `SPEC_APPROVED` / `ARCHITECTURE_READY` unless risk/scope escalates.

### Mode D: Autonomous (Future)
Not implemented. Any future autonomous runtime must be explicitly versioned and policy-gated. Automatic targeted/delegated selection by the parent within Mode A/B/C is already supported and is not Mode D.

## Mode Selection Priority
1. Prefer delegation-first routing by default.
2. Classify before selecting planning depth and delegation.
3. Use targeted delegation automatically for code-changing Tiny/Small and for artifact-generating review tasks when capability is available.
4. Escalate to Mode B when capability + adapters + task fit + planning gates all match.
5. Reject Mode B when eligibility checks fail, even if targeted is still required.
