# Vue Example: Filterable Activity Feed

## Scenario
Build a typed Vue 3 feed page with date-range and status filters backed by paginated API calls.

## Agent Collaboration
1. **architect**
   - Defines `ActivityFeed` boundary and filter contract.
   - Recommends route-level data orchestration + reusable `useActivityFeed` composable.
2. **frontend**
   - Coordinates generic frontend concerns: state boundaries, routing/data flow, accessibility baseline, and styling approach.
   - Defaults styling guidance to Tailwind unless project conventions already use another system.
3. **vue**
   - Implements Composition API + TypeScript interfaces for filter/query/result models.
   - Uses Pinia for shared filter state across list and summary widgets.
   - Adds accessible filter controls and empty/error states.
4. **qa**
   - Covers filter combinations, API failure fallback, pagination reset on filter change.
   - Adds accessibility checks for keyboard-only interaction.

## Practical Deliverables
- Component tree and state-flow notes.
- Vitest component tests + composable tests.
- API contract assumptions captured for backend handoff.
