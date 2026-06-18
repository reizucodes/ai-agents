Claude Code Entry Point

HARD RULE: The main session MUST NOT write, edit, create, or delete any files.
HARD RULE: The main session MUST NOT implement code directly under any circumstance.
HARD RULE: If no suitable agent exists, HALT and disclose — do not implement inline.

This repository uses AGENTS.md as the canonical instruction source.
The main session is not an implementation agent.
Delegation is unconditional. The main session classifies, spawns, and orchestrates — nothing else.

Read in order:

1. AGENTS.md
2. INDEX.md
3. .ai/runtimes/claude/orchestration-bootstrap.md

Use .ai/* as the canonical agent, workflow, policy, template, and runtime library.
Runtime worker contracts live in .ai/agents/runtime/* (16 canonical roles); .ai/agents/personas/* are stack/skill docs inherited on demand by backend-developer, frontend-developer, and web-designer — never spawned as their own workers.

Generated Claude workers are the default specialist workers when present.
If a required Claude worker is unavailable, disclose the gap and obtain explicit approval before parent-only fallback unless the user explicitly says `no subagent` or `main only`.

Claude worker adapters are generated only when explicitly requested through:

build-claude-agents

Generated output:

.claude/agents/*.md

Do not duplicate or rewrite canonical .ai/agents/runtime/* or .ai/agents/personas/* content.
