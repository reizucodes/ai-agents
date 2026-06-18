# React Example: Typed Order Dashboard Widget

## Scenario
Implement an order health dashboard widget with status counts, trend sparkline, and retry action.

## Role Collaboration (5-Phase Flow)

1. **Planning Council** (`project-owner`, `project-manager`, `dev-team-lead`, `ui-ux-designer`)
   - `dev-team-lead` defines separation: container handles data orchestration; presentational components stay pure.
   - Chooses optimistic retry with bounded rollback strategy.
   - `ui-ux-designer` confirms loading / empty / error states and keyboard interaction model.
2. **Implementation** (`frontend-developer` inheriting `.ai/agents/personas/react.md`)
   - Implements strongly typed hooks and component props.
   - Adds explicit loading / error / success states and keyboard-accessible actions.
   - Uses memoization only for expensive derived metrics.
   - Defaults styling to Tailwind unless project conventions already use another system.
3. **QA** (`qa-specialist`, `qa-team-lead`)
   - Tests stale data rendering, retry failure handling, and accessibility semantics.
   - Adds regression test for double-click retry race.
   - `qa-team-lead` signs off the Quality Gate.
4. **Business Go/No-Go** (`project-owner`, `project-manager`) — lightweight gate; collapses for Small work.
5. **Commit / PR** (`pr-manager`)
   - Produces the commit message, PR body, and change summary.

## Practical Deliverables
- Typed interface map for API responses and component props.
- Vitest coverage for hooks, rendering states, and retry interactions.
- Risk notes for API timeout handling.
- PR description by `pr-manager`.
