# OpenCode Agent Mapping

See `.ai/execution/adapter-role-mapping.md` for the canonical 16-role set, persona inheritance rules, and build workflow consumption.

Generated OpenCode agents are `.opencode/agents/<role>.md` in the consumer project, derived from `.ai/agents/runtime/<role>.md`. The `ai-agents` source repository must not commit `.opencode/agents/*`.
