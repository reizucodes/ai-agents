# Execution Modes Contract

## Purpose
Define execution modes for this instruction framework in a runtime-consumable format.

## Core Rules
- This instruction framework is not a runtime.
- Markdown files do not spawn agents.
- `.ai/agents/*` are canonical role contracts, not executable worker definitions.
- Sequential mode is the default and portable execution path.
- Delegated mode is optional, runtime-dependent, and requires explicit invocation.
- Pause/resume behavior for requirement ambiguity is governed by `.ai/policies/decision-gates.md` (Requirement Clarification Gate).

## Follow-Up Routing Rule
Every follow-up task must be reclassified before execution.

- Follow-up tasks inherit current feature context.
- Follow-up tasks do not inherit previous delegation decisions.
- Delegation must be decided from current classification and risk.

## Modes

### Mode A: Sequential (Default)
Use when:
- runtime has no reliable subagent support,
- task scope is small/tightly coupled,
- traceability is more important than parallelism.

Flow:
1. Load `AGENTS.md`.
2. Load `INDEX.md`.
3. Load `.ai/execution/task-classification.md` and classify task.
4. Select relevant `.ai/policies/*` and `.ai/workflows/*`.
5. Apply relevant `.ai/agents/*` role contracts serially in one runtime agent.
6. Implement, review, validate, and return output.

Requirements:
- All policy and quality gates still apply.
- No delegated/parallel claims unless runtime subagents were actually spawned.

### Mode B: Delegated (Optional)
Use only when:
- runtime explicitly supports subagents,
- runtime can isolate child context safely,
- parent agent explicitly invokes delegation,
- task decomposition can be assigned to disjoint ownership boundaries,
- classification and eligibility contracts allow delegation.

Flow:
1. Parent loads `AGENTS.md`, `INDEX.md`, execution/delegation contracts.
2. Parent reclassifies current task/follow-up using `.ai/execution/task-classification.md`.
3. Parent starts discovery/spec phase:
   - `project-manager` first,
   - `product-spec` second.
4. Parent runs user spec-discussion loop when required by Requirement Clarification Gate.
5. Parent produces a consolidated, implementation-ready spec handoff artifact.
6. Parent runs architecture phase with `architect` using the approved consolidated spec.
7. Parent assigns ownership boundaries from architect handoff.
8. Parent explicitly spawns implementation children (`backend`, `frontend`) only after discovery/spec and architecture gates are complete.
9. Children execute scoped tasks with mapped role contracts.
10. Parent runs validation/review/docs sequence:
   - `tester`,
   - `reviewer`,
   - `docs`.
11. Parent merges, validates, and returns final output.

Requirements:
- Delegated mode is never assumed implicitly.
- Child execution/concurrency depends on runtime capability.
- Parent remains accountable for final merge and gate compliance.
- For Medium/Large delegated work, implementation must not start before:
  - approved consolidated spec,
  - architecture handoff.

### Mode C: Autonomous (Future Extension)
- Not implemented in this instruction framework.
- Any future autonomous mode must be explicitly versioned and policy-gated.

## Targeted Follow-Up Delegation Mode
For follow-up tasks that do not require full planning rerun, use targeted delegation:
- Parent
- Relevant implementation agents only
- Reviewer if behavior changed
- Docs if docs/API/setup changed
- Parent final validation

Notes:
- Keep sequential mode as the portable default.
- Presence of markdown contracts does not create runtime spawning behavior.

Examples:
- CORS fix: backend; reviewer optional; docs optional.
- Backend endpoint + frontend view: backend + frontend + tester; reviewer; docs optional/required by change impact.
- Export report format update: docs or parent only; reviewer optional.

## Mode Selection Priority
1. Prefer sequential by default.
2. Use task classification before selecting planning depth and delegation.
3. Escalate to delegated only when capability and task fit are both true.
4. Reject delegated when eligibility checks fail.

## Planning and Diagram Guidance
- Tiny/Small: planning agents are optional; diagrams optional.
- Medium: planning agents expected; diagrams encouraged for workflow/ownership clarity.
- Large: planning agents mandatory; diagrams expected when boundaries/flows are non-trivial.

See:
- `.ai/execution/task-classification.md`
- `.ai/execution/capability-gates.md`
- `.ai/execution/delegation-eligibility.md`
- `.ai/delegation/*`
