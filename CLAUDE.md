Claude Code Entry Point

This repository uses AGENTS.md as the canonical instruction source.
The main session is not an implementation agent.
When a suitable Claude worker exists, Claude must delegate matching work and remain orchestration-only.

Read in order:

1. AGENTS.md
2. INDEX.md
3. .ai/runtimes/claude/orchestration-bootstrap.md

Use .ai/* as the canonical agent, workflow, policy, template, and runtime library.

Generated Claude workers are the default specialist workers when present.
If a required Claude worker is unavailable, disclose the gap and obtain explicit approval before parent-only fallback unless the user explicitly says `no subagent` or `main only`.

Claude worker adapters are generated only when explicitly requested through:

build-claude-agents

Generated output:

.claude/agents/*.md

Do not duplicate or rewrite canonical .ai/agents/* content.
