# OpenCode Delegation Mapping

## Canonical Flow (5-Phase)
Planning Council -> Implementation -> QA -> Business Go/No-Go -> Commit/PR.

## OpenCode Mapping (16-Role Set)
- **Primary orchestration agent**: `project-manager` (primary mode)
- **Planning Council subagents**: `project-owner`, `dev-team-lead`, `ui-ux-designer` (when UI surface), `cybersecurity-analyst` (when sensitive), `junior-project-manager` (clarification loops)
- **Implementation subagents**: `backend-developer`, `frontend-developer`, `database-administrator`, `devops-engineer`, `web-designer`
- **QA subagents**: `qa-specialist` (execution), `qa-team-lead` (consolidation + Quality Gate)
- **Business Go/No-Go subagents**: `project-owner` (risk acceptance), `devops-engineer` (deployment readiness)
- **Commit/PR subagents**: `pr-manager` (owns handoff), `documentation-writer`, `doc-team-lead`

## Persona Inheritance (Not Delegation)
Stack knowledge is delivered through persona inheritance, not separate workers.
- `backend-developer` loads `.ai/agents/personas/{laravel,fastapi,node-express,python}.md` on demand.
- `frontend-developer` loads `.ai/agents/personas/{react,vue}.md` on demand.
- `web-designer` loads `.ai/agents/personas/{react,vue}.md` on demand.

There is no separate `vue`, `react`, `laravel`, `fastapi`, `node-express`, or `python` runtime adapter to delegate to.

## Delegation Model
- Primary agent (`project-manager`) delegates via OpenCode subagent/task capability.
- Manual agent-targeted execution is available via `@agent` and command `agent`/`subtask` mapping.
- If subagent capability is unavailable, runtime must disclose the gap and obtain explicit approval before sequential fallback while preserving canonical gate/approval requirements.

## Governance Preservation
- Parent/orchestrator rules from `.ai/delegation/parent-orchestrator-contract.md` remain mandatory.
- Approval gates from `.ai/policies/approval-levels.md` (Auto / Recommendation / Approval) remain mandatory.
- Artifact requirements follow `.ai/execution/artifact-conventions.md` and `.ai/policies/risk-classification.md`.
