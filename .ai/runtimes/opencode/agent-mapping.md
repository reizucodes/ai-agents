# OpenCode Agent Mapping

## Canonical Source
Canonical runtime roles are defined only in `.ai/agents/runtime/*.md` (exactly 16 files; see `.ai/execution/adapter-role-mapping.md`).
Persona/skill docs live at `.ai/agents/personas/*.md` and are NEVER runtime adapter sources.

## Runtime Agent Files
OpenCode runtime agents are generated markdown files under `.opencode/agents/*.md` in the consumer project.
The `ai-agents` source repository must not commit `.opencode/agents/*`.

## Mapping (16 roles)
- `.opencode/agents/project-manager.md` -> `.ai/agents/runtime/project-manager.md` (primary orchestrator)
- `.opencode/agents/project-owner.md` -> `.ai/agents/runtime/project-owner.md`
- `.opencode/agents/junior-project-manager.md` -> `.ai/agents/runtime/junior-project-manager.md`
- `.opencode/agents/dev-team-lead.md` -> `.ai/agents/runtime/dev-team-lead.md`
- `.opencode/agents/backend-developer.md` -> `.ai/agents/runtime/backend-developer.md`
- `.opencode/agents/frontend-developer.md` -> `.ai/agents/runtime/frontend-developer.md`
- `.opencode/agents/database-administrator.md` -> `.ai/agents/runtime/database-administrator.md`
- `.opencode/agents/devops-engineer.md` -> `.ai/agents/runtime/devops-engineer.md`
- `.opencode/agents/ui-ux-designer.md` -> `.ai/agents/runtime/ui-ux-designer.md`
- `.opencode/agents/web-designer.md` -> `.ai/agents/runtime/web-designer.md`
- `.opencode/agents/cybersecurity-analyst.md` -> `.ai/agents/runtime/cybersecurity-analyst.md`
- `.opencode/agents/qa-specialist.md` -> `.ai/agents/runtime/qa-specialist.md`
- `.opencode/agents/qa-team-lead.md` -> `.ai/agents/runtime/qa-team-lead.md`
- `.opencode/agents/pr-manager.md` -> `.ai/agents/runtime/pr-manager.md`
- `.opencode/agents/documentation-writer.md` -> `.ai/agents/runtime/documentation-writer.md`
- `.opencode/agents/doc-team-lead.md` -> `.ai/agents/runtime/doc-team-lead.md`

## Persona Inheritance (Not Adapters)
The following persona files are inheritance-only and never generated as `.opencode/agents/*.md`:
- `.ai/agents/personas/laravel.md` -> inherited by `backend-developer` when Laravel/PHP detected
- `.ai/agents/personas/fastapi.md` -> inherited by `backend-developer` when FastAPI/Python detected
- `.ai/agents/personas/node-express.md` -> inherited by `backend-developer` when Node/Express detected
- `.ai/agents/personas/python.md` -> inherited by `backend-developer` when generic Python detected
- `.ai/agents/personas/react.md` -> inherited by `frontend-developer`, `web-designer` when React detected
- `.ai/agents/personas/vue.md` -> inherited by `frontend-developer`, `web-designer` when Vue detected

## Strategy Alignment
- OpenCode generation scope matches the canonical 16-role set defined in `.ai/execution/adapter-role-mapping.md`.
- Stack/framework knowledge is delivered through persona inheritance, not separate runtime workers.
- Generated output target is consumer-project `.opencode/agents/*` after running `.ai/workflows/build-opencode-agents.md`.

## Rules
- Adapter filenames must match the canonical runtime filename exactly (kebab-case, `.md`).
- Do not invent runtime-only role names. The 16 canonical roles are the complete set.
- Keep adapter prompts concise and runtime-specific.
- Preserve canonical boundaries, escalation paths, and policy references per `.ai/policies/*`.
