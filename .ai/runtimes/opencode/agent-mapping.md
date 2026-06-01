# OpenCode Agent Mapping

## Canonical Source
Canonical roles are defined only in `.ai/agents/*.md`.

## Runtime Agent Files
OpenCode runtime agents are generated markdown files under `.opencode/agents/*.md` in the consumer project.
The `ai-agents` source repository must not commit `.opencode/agents/*`.

## Mapping
- `.opencode/agents/project-manager.md` -> `.ai/agents/project-manager.md` (primary orchestrator)
- `.opencode/agents/product-spec.md` -> `.ai/agents/product-spec.md`
- `.opencode/agents/architect.md` -> `.ai/agents/architect.md`
- `.opencode/agents/backend.md` -> `.ai/agents/backend.md`
- `.opencode/agents/frontend.md` -> `.ai/agents/frontend.md`
- `.opencode/agents/qa.md` -> `.ai/agents/qa.md`
- `.opencode/agents/code-review.md` -> `.ai/agents/code-review.md`
- `.opencode/agents/docs.md` -> `.ai/agents/docs.md`
- `.opencode/agents/security.md` -> `.ai/agents/security.md`
- `.opencode/agents/devops.md` -> `.ai/agents/devops.md`

## Strategy Alignment
- OpenCode default generation scope matches established Claude/Codex orchestration-level adapters.
- Framework specialists remain canonical `.ai/agents/*` contracts referenced by orchestration agents (not runtime-native OpenCode adapters by default).
- Generated output target is consumer-project `.opencode/agents/*` after running `.ai/workflows/build-opencode-agents.md`.

## Rules
- Use canonical role names from `.ai/agents/*`.
- Do not invent runtime-only role names.
- Keep adapter prompts concise and runtime-specific.
- Preserve canonical boundaries and escalation paths.
