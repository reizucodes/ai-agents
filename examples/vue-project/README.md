# Vue Example: Filterable Activity Feed

## Scenario
Build a typed Vue 3 feed page with date-range and status filters backed by paginated API calls.

## Role Collaboration (5-Phase Flow)

1. **Planning Council** (`project-owner`, `project-manager`, `dev-team-lead`, `ui-ux-designer`)
   - `dev-team-lead` defines the `ActivityFeed` boundary and filter contract.
   - Recommends route-level data orchestration plus a reusable `useActivityFeed` composable.
   - `ui-ux-designer` confirms filter UX, empty / error states, and accessibility baseline.
2. **Implementation** (`frontend-developer` inheriting `.ai/agents/personas/vue.md`)
   - Implements Composition API + TypeScript interfaces for filter / query / result models.
   - Uses Pinia for shared filter state across list and summary widgets.
   - Adds accessible filter controls and empty / error states.
   - Defaults styling to Tailwind unless project conventions already use another system.
3. **QA** (`qa-specialist`, `qa-team-lead`)
   - Covers filter combinations, API failure fallback, pagination reset on filter change.
   - Adds accessibility checks for keyboard-only interaction.
   - `qa-team-lead` signs off the Quality Gate.
4. **Business Go/No-Go** (`project-owner`, `project-manager`) — lightweight gate.
5. **Commit / PR** (`pr-manager`)
   - Produces the commit message, PR body, and change summary.

## Practical Deliverables
- Component tree and state-flow notes.
- Vitest component tests + composable tests.
- API contract assumptions captured for backend handoff.
- PR description by `pr-manager`.
