# OpenCode Runtime Validation

## Manual Validation Checklist
1. Start OpenCode in repository root and confirm `AGENTS.md` is loaded.
2. Confirm `opencode.json` is detected and `instructions` entries are applied.
3. Confirm source repository does not contain committed `.opencode/agents/*`.
4. Verify source-repository guard behavior for `build-opencode-agents`:
   - fail/stop generation in `ai-agents` source repository by default
   - pass when refusal message is returned
   - pass when consumer/test project instructions are provided
   - fail if generation proceeds in source repository without explicit override
   - allow source-repo generation only when user explicitly provides unsafe override phrase
   - source detection uses strong indicators only (framework README identity, repo name `ai-agents`, framework git remote, optional marker file)
   - presence of `.ai/runtimes/*`, `.ai/workflows/build-opencode-agents.md`, and `examples/` alone must not trigger source-repo refusal
5. Verify consumer/test repository behavior:
   - invoking `build opencode agents` via `.opencode/commands/build-opencode-agents.md` executes generation immediately
   - no explicit path confirmation prompt
   - no extra user confirmation prompt
6. Confirm configured OpenCode agents appear, including `project-manager` as primary and the other 15 canonical roles as `subagent`.
7. Confirm OpenCode command list includes `/full-cycle`, `/spec-first`, `/regression-review`, `/review`, `/docs-update`, `/build-opencode-agents`.
8. Verify committed command files are startup-safe and do not require generated-agent frontmatter.
   - `grep -R "^agent:" .opencode/commands || true` returns no output
   - `grep -R "^subtask:" .opencode/commands || true` returns no output
9. Run a targeted command and verify it references canonical `.ai/workflows/*` paths.
10. Verify subagent delegation can invoke any of the 16 canonical mapped agents when generated adapters exist.
11. Verify generated OpenCode adapter scope matches the canonical 16-role set:
    - required generated set exists (validation fails if missing):
      `backend-developer`, `cybersecurity-analyst`, `database-administrator`, `dev-team-lead`, `devops-engineer`, `doc-team-lead`, `documentation-writer`, `frontend-developer`, `junior-project-manager`, `pr-manager`, `project-manager`, `project-owner`, `qa-specialist`, `qa-team-lead`, `ui-ux-designer`, `web-designer`
    - persona-derived adapters MUST be absent (validation fails if present):
      `laravel`, `fastapi`, `node-express`, `python`, `react`, `vue`
    - no invented runtime-only role names exist
12. Verify generated adapter frontmatter detection compatibility:
    - each generated adapter file starts with `---` on line 1
    - no metadata comments appear before frontmatter
    - frontmatter includes both `description` and `mode`
    - `mode: primary` only on `project-manager`; all others `mode: subagent`
    - generated metadata block exists after frontmatter and includes canonical references (`canonical-source: .ai/agents/runtime/<name>.md`)
    - each generated subagent body states it is not the main session and may execute role-defined work within assigned scope and policy gates
    - each generated subagent body instructs the worker to read `.ai/agents/runtime/<role>.md` before performing role work
13. Verify persona inheritance directives are present in inheriting adapters:
    - `backend-developer.md` references `.ai/agents/personas/{laravel,fastapi,node-express,python}.md`
    - `frontend-developer.md` references `.ai/agents/personas/{react,vue}.md`
    - `web-designer.md` references `.ai/agents/personas/{react,vue}.md`
14. Verify forbidden build side effects are absent:
    - `node_modules/` absent
    - `package.json` absent unless pre-existing and unrelated
    - `package-lock.json` absent unless pre-existing and unrelated
    - `pnpm-lock.yaml` absent unless pre-existing and unrelated
    - `yarn.lock` absent unless pre-existing and unrelated
    - `bun.lock`/`bun.lockb` absent unless pre-existing and unrelated
15. Distinguish OpenCode runtime-managed artifacts from framework outputs:
    - `.opencode/package.json`, `.opencode/package-lock.json`, `.opencode/node_modules/` may be created by OpenCode runtime/plugin execution
    - those runtime-managed artifacts are not `build-opencode-agents` outputs
    - `.opencode/agents/*.md` remain the only framework-generated OpenCode adapter artifacts
16. Re-run existing Claude/Codex runtime workflows and verify no behavior regression.

## Quick CLI Checks
- `opencode agent list`
- `opencode run --agent project-manager "Read AGENTS.md and summarize loaded workflow contracts"`
- In TUI: execute `/full-cycle <task>` and inspect routing.
- Scope/parity checks:
  - `build opencode agents` command executes directly in consumer/test repo (no confirmation gating)
  - `test ! -d .opencode/agents` in source repo
  - source repo invocation returns refusal message and consumer/test instructions
  - consumer repo with `.ai/runtimes/*` and project README still generates immediately
  - 16-role parity check:
    - `comm -3 <(printf '%s\n' backend-developer cybersecurity-analyst database-administrator dev-team-lead devops-engineer doc-team-lead documentation-writer frontend-developer junior-project-manager pr-manager project-manager project-owner qa-specialist qa-team-lead ui-ux-designer web-designer | sort) <(ls -1 /path/to/consumer-project/.opencode/agents/*.md | xargs -n1 basename | sed 's/\\.md$//' | sort)`
    - `comm -3 <(printf '%s\n' backend-developer cybersecurity-analyst database-administrator dev-team-lead devops-engineer doc-team-lead documentation-writer frontend-developer junior-project-manager pr-manager project-manager project-owner qa-specialist qa-team-lead ui-ux-designer web-designer | sort) <(jq -r '.agent | keys[]' opencode.json | sort)`
  - persona absence checks:
    - `ls -1 /path/to/consumer-project/.opencode/agents | rg '^(laravel|fastapi|node-express|python|react|vue)\\.md$'` returns no output
    - `jq -r '.agent | keys[]' opencode.json | rg '^(laravel|fastapi|node-express|python|react|vue)$' || true` returns no output
  - `head -1 /path/to/consumer-project/.opencode/agents/backend-developer.md | rg '^---$'`
  - `awk 'NR==1,/^---$/{print}' /path/to/consumer-project/.opencode/agents/backend-developer.md`
  - `grep 'canonical-source:' /path/to/consumer-project/.opencode/agents/*.md`
  - `rg 'not the main session' /path/to/consumer-project/.opencode/agents/*.md`
  - `rg 'Before performing role work, read and follow \\.ai/agents/runtime/' /path/to/consumer-project/.opencode/agents/*.md`
  - `test ! -d node_modules`
  - `test ! -f package.json`
  - `test ! -f package-lock.json`
  - `test ! -f pnpm-lock.yaml`
  - `test ! -f yarn.lock`
  - `test ! -f bun.lock`
  - `test ! -f bun.lockb`
