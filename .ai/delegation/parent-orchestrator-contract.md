# Parent Orchestrator Contract

## Purpose
Define what the parent/main session OWNS, what it MUST NOT do, and the handoff/merge-back rules during delegated execution. Policy details live in their canonical files; this contract cross-references rather than restates them.

## What the Parent OWNS
1. **Loading governance**: `AGENTS.md`, `INDEX.md`, relevant `.ai/policies/*`, `.ai/workflows/*`, `.ai/execution/*`, `.ai/delegation/*`.
2. **Classification** of every task and follow-up per `.ai/execution/task-classification.md`.
3. **Confidence-gate enforcement** per `.ai/execution/task-classification.md` (single rule).
4. **Approval-gate enforcement** per `.ai/policies/approval-levels.md` (Auto / Recommendation / Approval).
5. **Capability preflight** per `.ai/execution/capability-gates.md` before any targeted/delegated spawn.
6. **Planning Council orchestration** for Medium/Major work — collect outputs, validate required artifacts exist as files (per `.ai/execution/artifact-conventions.md`), present consolidated proposal package, request explicit implementation approval, wait for it.
7. **Ownership-boundary assignment** before spawning children (`.ai/delegation/ownership-boundaries.md`).
8. **Active-child registry** — idempotent spawn per role+scope; clear entries on exit/failure.
9. **Merge-back & final validation** per `.ai/delegation/merge-review-validation.md`.
10. **Final execution disclosure** — delegation used (yes/no), child roles invoked, parent-owned work, merge steps, run-report path for code-changing runs.
11. **Run-report production** — for code-changing runs, ensure `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md` exists. If `documentation-writer` was not invoked, parent/main writes it and marks producer as `parent/main`.
12. **Clarification consolidation** — if any child raises a clarification need, pause downstream delegation, consolidate the blocking question, ask the user, resume.

## What the Parent MUST NOT Do
- Claim delegated execution when none occurred.
- List a child role as used/completed unless it was actually spawned.
- Allow children to write outside assigned scope without reassignment.
- Bypass approval, quality, or planning gates.
- Skip planning gates for Medium/Major work.
- Continue from planning to implementation without explicit proposal approval.
- Treat silence, non-objection, or stage completion as approval.
- Spawn implementation roles (`backend-developer`, `frontend-developer`, etc.) for Medium/Major before `SPEC_APPROVED` and `ARCHITECTURE_READY`.
- Apply Medium/Major planning-state prohibitions to Tiny/Small targeted code changes unless risk/scope escalates.
- Silently collapse missing required adapters into parent/main.
- Implement directly when a suitable specialist exists, unless the user explicitly says `no subagent` / `main only`.
- Complete a code-changing run without producing the run report.

## Handoff Requirements (Parent → Child)
Each child handoff includes:
- canonical role + display label (format: `<display-name> [<canonical-role>]`, e.g. `Marcus [frontend-developer]`),
- explicit goals + acceptance criteria,
- ownership scope (files/modules/interfaces),
- forbidden paths,
- expected artifacts and output location,
- handoff inputs from prior phases.

## Merge-Back Rules (Child → Parent)
Each child returns:
- changed files,
- contract-impact summary,
- tests executed or pending,
- known risks/assumptions,
- artifact paths produced.

Parent then runs the merge/review/validation sequence in `.ai/delegation/merge-review-validation.md`.

## Delegated Workflow State
Parent maintains explicit state across the run:
`DISCOVERY` → `SPEC_DRAFT` → `SPEC_REVIEW` → `SPEC_APPROVED` → `ARCHITECTURE_READY` → `IMPLEMENTATION_ACTIVE` → `TESTING` → `REVIEW` → `DOCUMENTATION` → `COMPLETE`.

Tiny/Small targeted runs may skip planning states unless risk/scope escalates.

## Fallback Rule (Halt, Not Inline)
When parent cannot delegate because a required adapter is absent:
- disclose the missing adapter by name,
- halt and await explicit user instruction,
- do not implement inline as a silent fallback,
- do not claim delegation occurred,
- only proceed after explicit `no subagent` / `main only` instruction from the user.

Sequential role simulation is not an approved fallback for implementation work. The absence of an adapter requires a halt, not a workaround.

## Cross-References
- Gates and decision types → `.ai/policies/approval-levels.md`
- Classification + confidence gate + delegation matrix → `.ai/execution/task-classification.md`
- Capability preflight → `.ai/execution/capability-gates.md`
- Modes A/B/C/D → `.ai/execution/modes.md`
- Artifact requirement matrix + preflight output template → `.ai/execution/artifact-conventions.md`
- Ownership scopes → `.ai/delegation/ownership-boundaries.md`
- Merge/review/validation → `.ai/delegation/merge-review-validation.md`
- Child role mapping → `.ai/delegation/child-role-contracts.md`
