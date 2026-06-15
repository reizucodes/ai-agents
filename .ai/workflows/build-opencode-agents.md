# Build OpenCode Agents Workflow

## Purpose
Define an OpenCode-specific workflow for generating and validating markdown runtime adapters in `.opencode/agents/*.md` from canonical `.ai/agents/*` role contracts.

This workflow is a contract only. It does not generate files by itself.

## Scope
- Applies only when the user explicitly requests OpenCode adapter generation/validation work.
- Does not apply to normal feature, bugfix, refactor, review, or release tasks.
- This workflow is adapter generation only; it is not OpenCode runtime customization.

## Source-Repository Guard (Mandatory)
Before generating anything, determine whether execution is happening in the `ai-agents` framework source repository.

Treat repository as framework source only when one or more strong indicators are true:
- `README.md` identifies repository as the reusable AI agent library/framework source
- current repository directory name is `ai-agents`
- git remote URL clearly points to the `ai-agents` framework repository
- optional explicit source marker exists:
  - `.ai/framework-source.md`, or
  - `.ai/source-repository.md`

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
Generation inside framework source repository is allowed only when user explicitly states:
- `override: generate opencode agents in source repo`

Without that exact override intent, framework-source generation remains blocked.

## Invocation Rule
- The committed command entrypoint `.opencode/commands/build-opencode-agents.md` is sufficient user intent.
- In consumer/test repositories, execute generation immediately (no explicit path confirmation, no extra user confirmation).
- In source framework repository, apply mandatory refusal guard unless explicit unsafe override is provided.
- Do not invoke OpenCode customization/plugin flows (for example `customize-opencode`) for this workflow.

## Required Loaded Contracts
1. `AGENTS.md`
2. `INDEX.md`
3. `.ai/execution/runtime-adapter-contract.md`
4. `.ai/execution/adapter-role-mapping.md`
5. `.ai/execution/adapter-drift-validation.md`
6. `.ai/runtimes/opencode/adapter-schema.md`
7. relevant canonical `.ai/agents/*` files

## Default Output Set
Generate orchestration-level adapters:
- `project-manager`
- `product-spec`
- `architect`
- `backend`
- `frontend`
- `tester`
- `reviewer`
- `documentation`

Optional only by explicit request/policy:
- `security`
- `devops`
- `database` (if canonical role exists)

Do not generate runtime-native framework specialist adapters by default.

## Output Paths
- `.opencode/agents/*.md`
- `.ai/reports/opencode-adapter-run-report.md`

## Allowed Outputs Only
Allowed outputs for this workflow:
- `.opencode/agents/*.md`
- `.ai/reports/opencode-adapter-run-report.md`

Forbidden outputs/artifacts:
- `node_modules/`
- `package.json`
- `package-lock.json`
- `pnpm-lock.yaml`
- `yarn.lock`
- `bun.lockb`
- `bun.lock`
- any plugin installation artifacts
- any dependency installation artifacts

## Dependency/Plugin Prohibition
- Do not run package managers (`npm`, `pnpm`, `yarn`, `bun`) in this workflow.
- Do not install or scaffold OpenCode plugins in this workflow.
- Do not create project runtime/dependency manifests.

## Adapter Generation Rules
Each generated adapter must:
1. begin with markdown frontmatter as the first content in file:
   - `description`
   - `mode`
2. include metadata comments after frontmatter:
   - `generated-by: build-opencode-agents`
   - `generated-at`
   - `canonical-source`
   - `canonical-source-fingerprint`
   - `schema-contract: .ai/runtimes/opencode/adapter-schema.md`
3. include canonical-first body after metadata:
   - canonical source pointer
   - not-source-of-truth statement
   - canonical conflict rule
   - role identity
   - delegation triggers
   - ownership boundaries
   - deliverables and required artifacts
   - delegation contract

Runtime-facing alias mapping:
- `tester` -> canonical `.ai/agents/qa.md`
- `reviewer` -> canonical `.ai/agents/code-review.md`
- `documentation` -> canonical `.ai/agents/docs.md`

## Validation Steps
0. source-repo guard check:
   - detect strong source indicators only
   - fail generation unless explicit override phrase is present
   - confirm refusal message + consumer/test instructions are returned when blocked
1. canonical source existence check
2. metadata presence/format check
3. frontmatter-position check:
   - first line is `---`
   - no comments/content before frontmatter
4. metadata-position check:
   - metadata block appears after frontmatter
5. fingerprint drift check
6. frontmatter field check (`description`, `mode`)
7. canonical-first statement check
8. no-full-duplication check
9. output-set scope check against orchestration-level strategy
10. forbidden-artifact absence check:
   - `test ! -d node_modules`
   - `test ! -f package.json`
   - `test ! -f package-lock.json`
   - `test ! -f pnpm-lock.yaml`
   - `test ! -f yarn.lock`
   - `test ! -f bun.lockb`
   - `test ! -f bun.lock`
11. plugin/dependency-installation check:
   - no plugin/dependency installation commands executed
12. callable-worker validation:
   - generated workers identify ownership,
   - generated workers define delegation triggers,
   - generated workers define required deliverables/artifacts,
   - generated workers state that parent/main must delegate matching work when they are available

## Report Persistence Rule
Every `build-opencode-agents` run must write:
- `.ai/reports/opencode-adapter-run-report.md`

## Regeneration Invocation
Use runtime command/prompt:
- `Run .ai/workflows/build-opencode-agents.md and generate .opencode/agents/*.md from canonical .ai/agents/* using .ai/runtimes/opencode/adapter-schema.md, then write .ai/reports/opencode-adapter-run-report.md.`

Framework-source safety note:
- In the `ai-agents` source repository, this invocation must refuse by default unless explicit unsafe override is provided.
