# OpenCode Delegation Mapping

## Canonical Flow
`project-manager -> product-spec -> architect -> implementation -> tester -> reviewer -> documentation`

## OpenCode Mapping
- Primary orchestration agent: `project-manager` (primary mode)
- Planned subagents: `product-spec`, `architect`
- Implementation subagents: `backend`, `frontend` (and framework specialists when needed)
- Validation/review/documentation subagents: `tester`, `reviewer`, `documentation`
- Risk-based subagents: `security`, `devops` when canonical gates require

## Alias Mapping
- runtime-facing `tester` resolves to canonical `.ai/agents/qa.md`
- runtime-facing `reviewer` resolves to canonical `.ai/agents/code-review.md`
- runtime-facing `documentation` resolves to canonical `.ai/agents/docs.md`

## Delegation Model
- Primary agent delegates via OpenCode subagent/task capability.
- Manual agent-targeted execution is available via `@agent` and command `agent`/`subtask` mapping.
- If subagent capability is unavailable, runtime must disclose the gap and obtain explicit approval before sequential fallback while preserving canonical gate/approval requirements.

## Governance Preservation
- Parent/orchestrator rules from `.ai/delegation/parent-orchestrator-contract.md` remain mandatory.
- Proposal/approval gates from `.ai/policies/decision-gates.md` remain mandatory.
