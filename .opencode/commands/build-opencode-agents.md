---
description: Build OpenCode markdown agent adapters from canonical .ai/agents contracts.
---

Run canonical workflow:
- `.ai/workflows/build-opencode-agents.md`

Use canonical sources:
- `AGENTS.md`
- `INDEX.md`
- `.ai/agents/*.md`
- `.ai/runtimes/opencode/adapter-schema.md`

Allowed outputs:
- `.opencode/agents/*.md`
- `.ai/reports/opencode-adapter-run-report.md`

Forbidden outputs:
- `node_modules/`
- `package.json`
- `package-lock.json`
- `pnpm-lock.yaml`
- `yarn.lock`
- `bun.lock`
- `bun.lockb`
- plugin installation
- dependency installation

This command is startup-safe:
- no generated-agent frontmatter requirements
- no plugin or dependency setup actions
