# React Example: Typed Order Dashboard Widget

## Scenario
Implement an order health dashboard widget with status counts, trend sparkline, and retry action.

## Agent Collaboration
1. **architect**
   - Defines separation: container handles data orchestration; presentational components stay pure.
   - Chooses optimistic retry with bounded rollback strategy.
2. **frontend**
   - Coordinates generic frontend concerns: UI boundaries, state flow, accessibility baseline, and styling approach.
   - Defaults styling guidance to Tailwind unless project conventions already use another system.
3. **react**
   - Implements strongly typed hooks and component props.
   - Adds explicit loading/error/success states and keyboard-accessible actions.
   - Uses memoization only for expensive derived metrics.
4. **qa**
   - Tests stale data rendering, retry failure handling, and a11y semantics.
   - Adds regression test for double-click retry race.

## Practical Deliverables
- Typed interface map for API responses and component props.
- Vitest coverage for hooks, rendering states, and retry interactions.
- Risk notes for API timeout handling.
