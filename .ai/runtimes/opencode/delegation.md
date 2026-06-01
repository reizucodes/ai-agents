# OpenCode Delegation Mapping

## Canonical Flow
`project-manager -> product-spec -> architect -> implementation -> qa -> code-review -> docs`

## OpenCode Mapping
- Primary orchestration agent: `project-manager` (primary mode)
- Planned subagents: `product-spec`, `architect`
- Implementation subagents: `backend`, `frontend` (and framework specialists when needed)
- Validation/review/docs subagents: `qa`, `code-review`, `docs`
- Risk-based subagents: `security`, `devops` when canonical gates require

## Delegation Model
- Primary agent delegates via OpenCode subagent/task capability.
- Manual agent-targeted execution is available via `@agent` and command `agent`/`subtask` mapping.
- If subagent capability is unavailable, runtime falls back to sequential behavior while preserving canonical gate/approval requirements.

## Governance Preservation
- Parent/orchestrator rules from `.ai/delegation/parent-orchestrator-contract.md` remain mandatory.
- Proposal/approval gates from `.ai/policies/decision-gates.md` remain mandatory.
