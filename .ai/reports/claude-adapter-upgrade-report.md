# Claude Adapter Upgrade Regression Report

Date: 2026-05-29

## Scope
Phase correction: keep Claude adapter infrastructure and workflow contracts only. Do not ship generated `.claude/agents/*.md` in this phase.

## Files Changed
- `CLAUDE.md` (new)
- `.ai/runtimes/claude/adapter-schema.md` (new)
- `.ai/runtimes/claude/tool-mapping.md` (new)
- `.ai/runtimes/claude/orchestration-bootstrap.md` (new, updated)
- `.ai/workflows/build-claude-agents.md` (new, updated)
- `README.md` (updated)
- `docs/runtimes.md` (updated)
- `docs/workflows.md` (updated)
- `docs/agents.md` (updated)
- `.ai/reports/claude-adapter-upgrade-report.md` (updated)

## Removed Files
- `.ai/skills/bootstrap-claude.md`
- `.claude/agents/project-manager.md`
- `.claude/agents/product-spec.md`
- `.claude/agents/architect.md`
- `.claude/agents/backend.md`
- `.claude/agents/frontend.md`
- `.claude/agents/tester.md`
- `.claude/agents/reviewer.md`
- `.claude/agents/docs.md`
- `.ai/reports/claude-adapter-run-report.md`

## Validation Results
- Pass: Claude build infrastructure exists.
- Pass: `CLAUDE.md` exists as committed repository entrypoint.
- Pass: `bootstrap-claude` skill removed.
- Pass: No generated `.claude/agents/*.md` files are present in this phase.
- Pass: Actual Claude subagent generation is deferred to explicit `build-claude-agents` invocation.
- Pass: Canonical `.ai/agents/*` remain unchanged.

## Risks and Tradeoffs
- Claude adapter generation is now explicit-only; delegation acceleration depends on manual workflow invocation.
- `docs/*` files are currently ignored by `.gitignore`; documentation edits exist locally but may not be tracked.
- If canonical `.ai/agents/*` contracts evolve, generation behavior remains correct only when `build-claude-agents` is re-run.
