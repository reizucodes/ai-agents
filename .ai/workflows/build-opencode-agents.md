# Build OpenCode Agents Workflow

## Purpose
Define the OpenCode-specific workflow for generating and validating markdown runtime adapters in `.opencode/agents/*.md` from canonical runtime role contracts.

This workflow is a contract only. It does not generate files by itself.

## Scope
- Applies only when the user explicitly requests OpenCode adapter generation/validation work.
- Does not apply to normal feature, bugfix, refactor, review, or release tasks.
- This workflow is adapter generation only; it is not OpenCode runtime customization.

## Source-Repository Guard (Mandatory)
Before generating anything, determine whether execution is happening in the `ai-agents` framework source repository.

**Primary detection:** `.ai/.framework-root` exists at repo root → this is the framework source repo. This is the definitive check.

Supplementary indicators (use only when sentinel is absent and ambiguity exists):
- `README.md` identifies repository as the reusable AI agent library/framework source
- current repository directory name is `ai-agents`
- git remote URL clearly points to the `ai-agents` framework repository

Do not use these alone as source-repository indicators:
- `.ai/runtimes/claude/`
- `.ai/runtimes/codex/`
- `.ai/runtimes/opencode/`
- `.ai/workflows/build-opencode-agents.md`
- `examples/`

If framework source is detected, stop and refuse generation by default.
Do not create:
- `.opencode/agents/*`
- `.ai/reports/opencode-adapter-run-report.md`

Required refusal response:
"Refusing to generate .opencode/agents/* in the ai-agents source repository. Import the framework into a consumer/test project first, then run build-opencode-agents there."

Then provide this recommended test flow:

```bash
mkdir ../opencode-framework-test

rsync -avh \
  --exclude='.git' \
  --exclude='.DS_Store' \
  AGENTS.md \
  INDEX.md \
  CLAUDE.md \
  opencode.json \
  .ai \
  .opencode \
  examples \
  ../opencode-framework-test/
```

```bash
cd ../opencode-framework-test
opencode
```

Then run:
- `build opencode agents`

## Override Rule (Unsafe / Diagnostic Only)
Generation inside the framework source repository is allowed only when the user explicitly states:
- `override: generate opencode agents in source repo`

Without that exact override intent, framework-source generation remains blocked.

## Invocation Rule
- The committed command entrypoint `.opencode/commands/build-opencode-agents.md` is sufficient user intent.
- In consumer/test repositories, execute generation immediately (no explicit path confirmation, no extra user confirmation).
- In the source framework repository, apply the mandatory refusal guard unless an explicit unsafe override is provided.
- Do not invoke OpenCode customization/plugin flows for this workflow.

## Canonical and Adapter Clarifications
- `AGENTS.md` and `.ai/*` remain canonical.
- `.opencode/agents/*.md` are generated OpenCode runtime adapters only.
- Canonical adapter sources are `.ai/agents/runtime/*.md` (exactly 16 files).
- `.ai/agents/personas/*.md` are NEVER adapter sources. They are inheritance-only skill docs loaded on demand by matching runtime agents.
- `.ai/execution/adapter-role-mapping.md` is the authoritative 16-role list.

## Required Loaded Contracts
1. `AGENTS.md`
2. `INDEX.md`
3. `.ai/execution/runtime-adapter-contract.md`
4. `.ai/execution/adapter-role-mapping.md`
5. `.ai/execution/adapter-drift-validation.md`
6. `.ai/runtimes/opencode/adapter-schema.md`
7. `.ai/runtimes/opencode/agent-mapping.md`
8. `.ai/policies/approval-levels.md`
9. `.ai/policies/quality-gates.md`
10. `.ai/policies/risk-classification.md`
11. The 16 canonical role source files from `.ai/agents/runtime/*.md`
12. Persona files at `.ai/agents/personas/*.md` (used only to describe inheritance, not as adapter sources)

## Canonical Output Set (16)
Generate exactly 16 adapters at `.opencode/agents/<role>.md`, with filenames matching the canonical runtime filenames:
`backend-developer`, `cybersecurity-analyst`, `database-administrator`, `dev-team-lead`, `devops-engineer`, `doc-team-lead`, `documentation-writer`, `frontend-developer`, `junior-project-manager`, `pr-manager`, `project-manager`, `project-owner`, `qa-specialist`, `qa-team-lead`, `ui-ux-designer`, `web-designer`.

`project-manager` is generated with `mode: primary`. The other 15 are `mode: subagent`.

Build workflow MUST refuse to generate any adapter whose name is not in this list. Build workflow MUST NOT generate adapters from `.ai/agents/personas/*`.

## Persona Inheritance Documentation
For the three inheriting roles, the generated adapter's body must describe how the agent loads persona files on demand when task detection matches a stack:

- `backend-developer`: load `.ai/agents/personas/{laravel,fastapi,node-express,python}.md` on demand when the matching backend stack is detected.
- `frontend-developer`: load `.ai/agents/personas/{react,vue}.md` on demand when the matching frontend framework is detected.
- `web-designer`: load `.ai/agents/personas/{react,vue}.md` on demand when the rendered web surface uses the matching JS framework.

Persona files are NEVER copied into the adapter body and are NEVER emitted as `.opencode/agents/*.md`.

## Output Paths
- `.opencode/agents/<role>.md` (16 files)
- `.ai/reports/opencode-adapter-run-report.md`

## Allowed Outputs Only
Allowed outputs for this workflow:
- `.opencode/agents/<role>.md`
- `.ai/reports/opencode-adapter-run-report.md`

Forbidden outputs/artifacts:
- `node_modules/`
- `package.json`, `package-lock.json`
- `pnpm-lock.yaml`
- `yarn.lock`
- `bun.lockb`, `bun.lock`
- any plugin installation artifacts
- any dependency installation artifacts

## Dependency/Plugin Prohibition
- Do not run package managers (`npm`, `pnpm`, `yarn`, `bun`) in this workflow.
- Do not install or scaffold OpenCode plugins in this workflow.
- Do not create project runtime/dependency manifests.

## Adapter Generation Rules
Each generated adapter must:
1. Begin with markdown frontmatter as the first content in the file:
   - `description`
   - `mode` (`primary` for `project-manager`, `subagent` for the other 15)
2. Include metadata comments immediately after frontmatter:
   - `generated-by: build-opencode-agents`
   - `generated-at` (UTC ISO-8601)
   - `canonical-source` (`.ai/agents/runtime/<name>.md`)
   - `canonical-source-fingerprint` (`sha256:<hex>`)
   - `schema-contract: .ai/runtimes/opencode/adapter-schema.md`
3. Include canonical-first body after metadata:
   - explicit child-scope guard: worker is not the main session
   - canonical source pointer
   - explicit instruction to read `.ai/agents/runtime/<name>.md` before performing role work
   - not-source-of-truth statement
   - canonical conflict rule
   - explicit statement that role-defined work is allowed within assigned scope and policy gates
   - role identity
   - delegation triggers
   - ownership boundaries
   - deliverables and required artifacts (per `.ai/execution/artifact-conventions.md`)
   - delegation contract
   - persona inheritance directive (only for `backend-developer`, `frontend-developer`, `web-designer`)
   - references to `.ai/policies/approval-levels.md`, `.ai/policies/quality-gates.md`, `.ai/policies/risk-classification.md`, `.ai/policies/definition-of-done.md`, `.ai/policies/secrets-management.md`

## Approval and Gate Model
Generated adapters must reference the 3-gate model defined in `.ai/policies/approval-levels.md`:
- **Auto**: runtime proceeds without human input.
- **Recommendation**: present options, default to safe path, log decision.
- **Approval**: halt and require explicit user approval before proceeding.

Generated adapters must also:
- state that they are delegated child agents, not the main session
- require reading `.ai/agents/runtime/<role>.md` before role work
- state that role-defined work is allowed within assigned scope and policy gates

## Validation Steps
0. Source-repo guard check:
   - check `.ai/.framework-root` at repo root first (definitive)
   - fall back to supplementary indicators only when sentinel is absent
   - fail generation unless explicit override phrase is present
   - confirm refusal message + consumer/test instructions are returned when blocked
1. Role-set integrity: exactly 16 outputs, names match `adapter-role-mapping.md`.
2. No outputs derived from `.ai/agents/personas/*`.
3. Canonical source existence check for each adapter (`.ai/agents/runtime/<name>.md`).
4. Metadata presence/format check.
5. Frontmatter-position check (first line is `---`).
6. Metadata-position check (metadata block appears after frontmatter).
7. Fingerprint drift check.
8. Frontmatter field check (`description`, `mode`).
9. `mode: primary` only on `project-manager`; all others `mode: subagent`.
10. Canonical-first statement check.
11. No full canonical-role duplication.
12. Persona inheritance directive present for `backend-developer`, `frontend-developer`, `web-designer`.
13. Forbidden-artifact absence check:
    - `test ! -d node_modules`
    - `test ! -f package.json`
    - `test ! -f package-lock.json`
    - `test ! -f pnpm-lock.yaml`
    - `test ! -f yarn.lock`
    - `test ! -f bun.lockb`
    - `test ! -f bun.lock`
14. Plugin/dependency-installation check: no plugin/dependency installation commands executed.
15. Callable-worker validation:
    - generated workers identify ownership
    - generated workers define delegation triggers
    - generated workers define required deliverables/artifacts
    - generated workers state that parent/main must delegate matching work when they are available
    - generated workers state that they are not the main session
    - generated workers instruct the worker to read `.ai/agents/runtime/<role>.md` before role work
    - generated workers state that role-defined work is allowed within assigned scope and policy gates

## Rejection Conditions
Reject generation when any apply:
- `.ai/.framework-root` present at repo root without explicit override phrase
- adapter name not in canonical 16-role set
- attempt to generate an adapter from `.ai/agents/personas/*`
- missing canonical source at `.ai/agents/runtime/<name>.md`
- missing/invalid frontmatter or metadata
- full canonical-role duplication
- source-repo guard triggered without explicit override
- any forbidden artifact created during run

## Report Persistence Rule
Every `build-opencode-agents` run must write:
- `.ai/reports/opencode-adapter-run-report.md`

## Regeneration Invocation
- `Run .ai/workflows/build-opencode-agents.md and generate the 16 adapters at .opencode/agents/*.md from canonical .ai/agents/runtime/* using .ai/execution/adapter-role-mapping.md and .ai/runtimes/opencode/adapter-schema.md, then write .ai/reports/opencode-adapter-run-report.md.`

Framework-source safety note:
- In the `ai-agents` source repository, this invocation must refuse by default unless explicit unsafe override is provided.
