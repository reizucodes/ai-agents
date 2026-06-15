# AI Agent Library

## Purpose
A reusable, runtime-agnostic agent and workflow library for AI coding assistants (Claude Code, Codex, OpenCode). It defines 16 canonical roles, 6 inheritable stack personas, 4 workflows, and a small governance layer that move work from idea to commit with less manual coordination.

## Installation
### Option 1: Bootstrap a New Project
Use this when starting a brand-new project from the framework.

```bash
git clone <repo-url> <project-folder>
cd <project-folder>

rm -rf .git
git init
```

After initialization, the new project owns its own Git history. The framework repository is no longer required.

Runtime assets retained in the project:

```txt
AGENTS.md
INDEX.md
CLAUDE.md
opencode.json
.ai/
.opencode/
examples/
```

The framework `README.md` may be replaced with project-specific documentation.

### Option 2: Install Into an Existing Repository
Use this when integrating the framework into an already-existing codebase.

Preview first:

```bash
rsync -avh --dry-run --itemize-changes \
  --exclude='.DS_Store' \
  AGENTS.md \
  INDEX.md \
  CLAUDE.md \
  opencode.json \
  .ai \
  .opencode \
  examples \
  /path/to/existing-project/
```

Then perform the sync:

```bash
rsync -avh \
  --exclude='.DS_Store' \
  AGENTS.md \
  INDEX.md \
  CLAUDE.md \
  opencode.json \
  .ai \
  .opencode \
  examples \
  /path/to/existing-project/
```

Always run the dry-run command first. `.ai` and `examples` intentionally do not use trailing slashes (in `rsync`, a trailing slash copies directory contents; no trailing slash copies the directory itself).

`CLAUDE.md` is the Claude Code runtime entrypoint. `AGENTS.md` is the canonical instruction source. `.ai/*` is the canonical workflow / agent / runtime library.

### Claude Code Runtime Installation

```bash
rsync -avh \
  --exclude='.DS_Store' \
  AGENTS.md \
  INDEX.md \
  CLAUDE.md \
  .ai \
  examples \
  ../your-project/
```

Notes:
- `.claude/agents/*.md` is not installed by default.
- Claude adapters are generated later through `build-claude-agents` (16 adapters).

### OpenCode Runtime Installation

```bash
rsync -avh \
  --exclude='.DS_Store' \
  AGENTS.md \
  INDEX.md \
  opencode.json \
  .ai \
  .opencode \
  examples \
  ../your-project/
```

Notes:
- `opencode.json` is the OpenCode runtime configuration (16-role `agent:` block, `project-manager` primary, other 15 subagents).
- `.opencode/commands/*` are committed runtime entrypoints.
- `.opencode/agents/*` is generated later through `build-opencode-agents`.

The following are not part of the runtime installation payload:

```txt
README.md
docs/
```

Existing source code, Git history, CI/CD, and project documentation remain untouched.

### First Steps
1. Read `INDEX.md`.
2. Provide a goal (vague or specific).
3. Keep the main session orchestration-only (`project-manager`) and let it delegate matching work.
4. Pause only when a Recommendation or Approval gate is triggered.

## Usage
1. Start from `INDEX.md` and let the main session classify the task.
2. The main session walks the 5-phase flow (Planning Council → Implementation → QA → Business Go/No-Go → Commit/PR) and delegates per phase.
3. Run a workflow from `.ai/workflows/` (feature, bugfix, refactor, release).
4. Use templates from `.ai/templates/` for required artifacts.
5. Use `examples/` as reference scenarios.

## Available Commands
| Command | Purpose |
| --- | --- |
| `build-claude-agents` | Generate the 16 Claude subagent adapters from `.ai/agents/runtime/*`. |
| `build-codex-agents` | Generate the 16 Codex agent adapters from `.ai/agents/runtime/*`. |
| `build-opencode-agents` | Generate the 16 OpenCode subagent adapters from `.ai/agents/runtime/*`. |
| `build-project-intelligence` | Generate or refresh repository context so agents understand the codebase, architecture, conventions, integrations, testing strategy, and technical debt. |

## Agents

The framework defines **16 canonical runtime roles** in `.ai/agents/runtime/` and **6 inheritable stack personas** in `.ai/agents/personas/`. The runtime files are the only source for adapter generation. Personas are read on demand and never spawned as adapters.

Role groupings (full reference in [docs/agents.md](docs/agents.md)):

- **Planning Council** — `project-owner`, `project-manager`, `junior-project-manager`, `dev-team-lead`, `ui-ux-designer`, `cybersecurity-analyst`
- **Implementation** — `backend-developer`, `frontend-developer`, `database-administrator`, `devops-engineer`, `web-designer`
- **QA** — `qa-specialist`, `qa-team-lead`
- **Commit / PR** — `pr-manager`, `documentation-writer`, `doc-team-lead`

Persona inheritance: `backend-developer` inherits `.ai/agents/personas/<laravel|fastapi|node-express|python>.md` when the task matches the stack. `frontend-developer` inherits `.ai/agents/personas/<vue|react>.md`. `web-designer` may inherit relevant frontend personas. Stack files are skill inheritance, not separate workers.

## Workflows

Four workflows, all following the same 5-phase flow:
- `feature.md` — new behavior / new capability
- `bugfix.md` — defect repair with regression coverage
- `refactor.md` — internal improvement with behavior preserved
- `release.md` — production rollout with rollback and monitoring

Tiny work collapses Phase 1 to `project-manager` only. Major work runs the full Planning Council.

## Artifact Requirements

Artifacts are required only for major or sensitive work, per `.ai/execution/artifact-conventions.md`:

| Work type | Required artifacts |
|---|---|
| Tiny / Small (non-sensitive) | none beyond conversation outputs |
| Medium feature | `feature-spec.md` + run-report |
| Major feature / major fix / major refactor | `feature-spec.md` + `technical-design.md` + run-report + proposal review package |
| Schema change | `technical-design.md` + migration / rollback notes |
| Security-sensitive | `threat-model.md` + proposal review package |
| Deployment-impacting | `release.md` workflow + run-report |
| PR / release handoff | PR description by `pr-manager` + run-report |

## Governance

Three gate types defined in `.ai/policies/approval-levels.md`:
- **Auto** — runtime proceeds, no human input.
- **Recommendation** — runtime presents options, defaults to the safe path, logs the decision.
- **Approval** — runtime halts and requires explicit user approval before proceeding.

Surviving policies under `.ai/policies/`:
- `approval-levels.md` — three gate types + risky-action templates
- `quality-gates.md` — Quality Gate (owned by `qa-team-lead`) and Release Gate (owned by `pr-manager`)
- `risk-classification.md` — Tiny / Small / Medium / Major + schema / deploy / security tiers
- `definition-of-done.md` — per-role and per-work-item completion criteria
- `secrets-management.md` — credential and sensitive-data handling

## Runtime Adapters

The framework is runtime-agnostic. Each runtime build workflow reads sources **only** from `.ai/agents/runtime/*` and emits **16** adapter files:

| Runtime | Adapter Output | Build Workflow |
|---|---|---|
| Claude Code | `.claude/agents/*.md` | `build-claude-agents` |
| Codex | `.codex/agents/*.toml` | `build-codex-agents` |
| OpenCode | `.opencode/agents/*.md` | `build-opencode-agents` |

Persona files under `.ai/agents/personas/*` are never generated as adapters; runtime roles inherit them on demand when the task matches the stack.

Repository default state:
- ships with `AGENTS.md`, `INDEX.md`, `CLAUDE.md`, `opencode.json`, and runtime contracts under `.ai/runtimes/{claude,codex,opencode}/*`
- does not ship with `.claude/agents/*`, `.codex/agents/*`, or `.opencode/agents/*`

Generated adapters are derivative artifacts. Canonical sources remain `.ai/agents/runtime/*`.

## First Claude Code Session
1. Open Claude Code in the project.
2. Claude reads `CLAUDE.md`, `AGENTS.md`, `INDEX.md`.
3. Claude discovers runtime contracts under `.ai/runtimes/claude/*`.
4. Claude can execute `build-claude-agents`.
5. Generated adapters appear under `.claude/agents/*.md` (16 files).

## OpenCode Runtime Usage

OpenCode reads `AGENTS.md` and `opencode.json` `instructions` to load canonical `.ai/*` contracts. `.opencode/commands/*.md` are source-level entrypoints. Natural invocation `build opencode agents` is handled by `.opencode/commands/build-opencode-agents.md`, which routes to `.ai/workflows/build-opencode-agents.md` and generates static markdown adapters.

In the `ai-agents` source repository, `build-opencode-agents` refuses generation by default. Use a consumer/test project; source-repo generation requires explicit override: `override: generate opencode agents in source repo`.

OpenCode may also create runtime-managed dependency artifacts under `.opencode/` during startup or command execution (e.g. `.opencode/package.json`, `.opencode/node_modules/`). Those are OpenCode-owned and not generated by `ai-agents`.

Consumer/test generation flow:

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

Then run: `build opencode agents`.

### Runtime Routing Metadata
Runtime metadata may label the routing shape as `auto`, `sequential`, `targeted`, or `delegated`, but that metadata is secondary. Framework default behavior:
- the main session is not an implementation agent,
- matching specialist work is delegated by default,
- parent-only implementation requires explicit user instruction (`no subagent`, `main only`) or explicit approval after disclosed fallback,
- pure non-code-changing analysis/review work may remain parent-only.

### Agent Display Names
Generated agents self-identify as:

```text
<nickname> [<canonical-role>]
```

Examples:
- `Beacon [project-manager]`
- `Anchor [backend-developer]`
- `Canvas [frontend-developer]`
- `Scout [qa-specialist]`
- `Audit [pr-manager]`
- `Quill [documentation-writer]`

### Adapter Governance
- `AGENTS.md` remains canonical.
- Adapter drift validation (`.ai/execution/adapter-drift-validation.md`) applies to all three runtimes.
- Generated adapters under `.claude/agents/*`, `.codex/agents/*`, `.opencode/agents/*` are derived from `.ai/agents/runtime/*` only.

## Workflow Philosophy
Scale process according to risk:
- Tiny / Small work runs on the fast path with no required artifacts.
- Medium / Major work runs the full 5-phase flow with the artifacts the matrix demands.
- Security-sensitive and deployment-impacting work always engages the appropriate specialist (`cybersecurity-analyst`, `devops-engineer`).
- Pause for user input only at Recommendation or Approval gates.

## Templates
- `task.md` — Tiny / Small daily work items
- `feature-spec.md` — Medium and Major feature definition
- `technical-design.md` — Major work and schema changes
- `adr.md` — lightweight architecture decision record
- `pr-review.md` — structured review output consumed by `pr-manager`
- `test-plan.md` — test design and execution planning
- `threat-model.md` — security-sensitive change artifact

## Examples
- `laravel-project` — backend feature collaboration (`backend-developer` + laravel persona + `qa-specialist` + `pr-manager`)
- `vue-project` — frontend feature collaboration (`frontend-developer` + vue persona + `qa-specialist`)
- `react-project` — frontend feature collaboration (`frontend-developer` + react persona + `qa-specialist`)
- `fastapi-project` — API feature collaboration (`backend-developer` + fastapi persona + `qa-specialist` + `pr-manager`)

## Customization
- Keep role sections intact (`Role`, `Responsibilities`, `Constraints`, etc.).
- Add stack-specific rules to the relevant persona file under `.ai/agents/personas/`, not to runtime roles.
- Extend workflows by adding explicit handoff contracts, not vague instructions.

## Contribution Guidelines
1. Add or update one role / persona / workflow / template per change.
2. Preserve required section headers for compatibility.
3. Include at least one practical example update when behavior changes.
4. Ensure strong typing, testing, and API contract guidance remain explicit.
