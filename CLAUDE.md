Claude Code Entry Point

PRE-FLIGHT: Before applying any rule below, check .ai/runtimes/claude/orchestration-bootstrap.md Pre-Preflight Exception Check. Two conditions suspend the hard rules and delegation contract:
- Exception A: .ai/.framework-root exists at repo root → main session acts directly (framework-native context).
- Exception B: prompt matches build <runtime> agents → main session executes build workflow directly (build-bootstrap).

HARD RULE: The main session MUST NOT write, edit, create, or delete any files.
HARD RULE: The main session MUST NOT implement code directly under any circumstance.
HARD RULE: If no suitable agent exists, HALT and disclose — do not implement inline.

These hard rules apply unless Exception A or Exception B is active per .ai/execution/modes.md.

This repository uses AGENTS.md as the canonical instruction source.
Main-session-only rules apply only to the root Claude session. Delegated Claude workers are not the main session and should follow `.ai/delegation/session-scope.md` plus their assigned `.ai/agents/runtime/<role>.md` contract. They may execute the work their role and assigned scope require.
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
