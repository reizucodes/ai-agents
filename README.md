# Reusable AI Agent Library

## Purpose
This library provides reusable, production-oriented workflow agents for coding assistants (Codex, Claude Code, Cursor, Cline, Roo Code). It is designed to be copied into software projects and used immediately for implementation, review, testing, and release workflows.

## Installation
## Option 1: Bootstrap a New Project
Use this when starting a brand-new project from the framework.

```bash
git clone <repo-url> <project-folder>
cd <project-folder>

rm -rf .git
git init
```

This workflow treats the framework repository as a bootstrap scaffold.
After initialization, the new project owns its own Git history.
The framework repository is no longer required after bootstrap.

The runtime assets retained in the project are:

```txt
AGENTS.md
INDEX.md
.ai/
examples/
```

The framework `README.md` may be replaced with project-specific documentation.

Example project structure:

```txt
profile-dashboard/
├── .ai/
├── examples/
├── AGENTS.md
├── INDEX.md
├── README.md
├── src/
├── package.json
└── ...
```

## Option 2: Install Into an Existing Repository
Use this when integrating the framework into an already-existing codebase.

Preview first:

```bash
rsync -avh --dry-run --itemize-changes \
  --exclude='.DS_Store' \
  AGENTS.md INDEX.md .ai examples \
  /path/to/existing-project/
```

Then perform the sync:

```bash
rsync -avh \
  --exclude='.DS_Store' \
  AGENTS.md INDEX.md .ai examples \
  /path/to/existing-project/
```

Always run the dry-run command first.
`.ai` and `examples` intentionally do not use trailing slashes.
In `rsync`, a trailing slash copies directory contents, while no trailing slash copies the directory itself. See: https://stackoverflow.com/questions/20300971/rsync-copy-directory-contents-but-not-directory-itself

The goal is to preserve:

```txt
AGENTS.md
INDEX.md
.ai/
examples/
```

inside the destination repository.

The following are not part of the runtime installation payload:

```txt
README.md
docs/
```

Existing source code remains untouched.
Existing Git history remains untouched.
Existing CI/CD remains untouched.
Existing project documentation remains untouched.

### First Steps
1. Read `INDEX.md`.
2. Provide a project goal or idea.
3. Allow workflow progression through:
   - Ideation
   - Product Specification
   - Architecture
   - Implementation Planning
   - Implementation
4. Only intervene when:
   - Decision Gates are triggered
   - Approval Decisions are required

## Usage
1. Pick the relevant role file in `.ai/agents/`.
2. Run a workflow from `.ai/workflows/` (feature, bugfix, refactor, release).
3. Start from templates in `.ai/templates/` for consistent specs, reviews, and tests.
4. Use `examples/` as reference implementations of multi-agent handoffs.
5. Use `INDEX.md` first for risk-scaled quick-start guidance.

## Repository Runtime Assets
Repository ships with:
- `AGENTS.md`
- `INDEX.md`
- `CLAUDE.md`
- Claude runtime contracts under `.ai/runtimes/claude/*`

Repository does not ship with:
- `.claude/agents/*.md`

Claude adapters are generated only through:
- `build-claude-agents`

## Codex Runtime Usage
Generated Codex agents are artifacts under `.codex/agents/*.toml`.  
Canonical process/role contracts remain in `.ai/*`.

## Claude Runtime Usage
Generated Claude adapters are project-level subagent artifacts under `.claude/agents/*.md`.  
Canonical process/role contracts remain in `.ai/*`.

Constraints:
- `.claude/agents/*.md` are derivative runtime adapters, not canonical role definitions.
- `~/.claude/agents/` (user-level Claude agents) is outside this repository scope.
- Claude adapter `description` fields are delegation-critical and should stay task-oriented.
- Claude adapter `tools` fields should remain least-privilege and role-scoped.

Adapter governance:
- `AGENTS.md` remains canonical.
- Adapter drift validation applies to both Codex and Claude adapter outputs.
- Codex adapters (`.codex/agents/*.toml`) and Claude adapters (`.claude/agents/*.md`) are generated artifacts derived from `.ai/agents/*`.

### Runtime Modes
- `auto`: framework default; parent classifies and chooses best mode.
- `sequential`: parent/main-only execution.
- `targeted`: only relevant agents for focused scope (for example reviewer/docs or backend-only).
- `delegated`: full orchestration flow across required roles.

### Practical Codex Invocation
For reliable subagent spawning in Codex, include explicit activation wording:

```md
Use the available Codex subagents.
Do not complete this as single-agent work.
```

### Recommended Delegated Example

```md
Execution mode: delegated
Use the available Codex subagents.
Do not complete this as single-agent work.

Task:
...
```

### Recommended Targeted Example

```md
Execution mode: targeted
Use the available Codex subagents.

Task:
...
```

### When To Use Each Mode
- `sequential`:
  - Q&A
  - simple inspection
  - analysis-only work
- `targeted`:
  - focused fixes
  - review tasks
  - docs/test/backend/frontend-only work
- `delegated`:
  - Medium/Large work
  - planning/spec/architecture/implementation/testing/review/docs flows
- `auto`:
  - framework default
  - Codex may still require explicit subagent activation wording

### Agent Display Names
Generated agents should self-identify as:

```text
<nickname> [<canonical-role>]
```

Examples:
- `Beacon [project-manager]`
- `Scribe [product-spec]`
- `Forge [architect]`
- `Anchor [backend]`
- `Canvas [frontend]`
- `Scout [tester]`
- `Audit [reviewer]`
- `Quill [docs]`

### Runtime Limitation Note
- Runtime thread names may still be platform-controlled.
- The `[role]` suffix is authoritative.
- Explicit subagent-usage wording is currently the most reliable trigger for delegation.

## Workflow Philosophy
Scale process according to risk:
- Fast path for small work.
- Structured path for complex work.
- Security path for sensitive work.
- Reduce prompt burden on users through autonomous stage progression.
- Preserve user control for meaningful product, architecture, scope, security, and cost decisions.

Recommended discovery-to-delivery sequence:
Idea -> Ideation -> Product Spec -> Architect -> Implementation

Workflow behavior expectation:
- Users provide goals (even if vague).
- Agents progressively refine ideas into specs, architecture, implementation plans, and code.
- User interruption should occur only when Decision Gates are triggered.

## Agent Architecture
- `ideation.md`: discovery and concept shaping before product specification.
- `product-spec.md`: requirement quality, acceptance criteria, and implementation-ready scope.
- `architect.md`: system design, boundaries, tradeoffs, risk.
- `frontend.md`, `backend.md`: generic orchestration for cross-cutting frontend/backend decisions and delegation.
- `laravel.md`, `vue.md`, `react.md`, `node-express.md`, `python.md`, `fastapi.md`: stack-specific delivery.
- `security.md`: advisory security review across design, implementation, and release risk.
- `qa.md`: test strategy and risk-based validation.
- `code-review.md`: rigorous review gate.
- `devops.md`: delivery and operational readiness.

## Workflow Architecture
- `feature.md`: new feature delivery from product spec to merge-ready state.
- `bugfix.md`: triage, root cause, safe remediation, regression coverage.
- `refactor.md`: structure improvement with behavior protection.
- `release.md`: production release readiness and rollback planning.

## Governance Policies
- `.ai/policies/approval-levels.md`: Level 0/1/2 execution controls.
- `.ai/policies/runtime-safety.md`: allowed vs approval-required vs explicit-command actions.
- `.ai/policies/quality-gates.md`: architecture, implementation, quality, and release gates.
- `.ai/policies/risk-classification.md`: Low/Medium/High/Critical risk model.
- `.ai/policies/definition-of-done.md`: global and stack-specific completion criteria.
- `.ai/policies/secrets-management.md`: credential and sensitive-data handling rules.

## Future Extension Points
- `.ai/skills/`: future reusable knowledge modules (create only when repeated guidance justifies extraction).
- `.ai/hooks/`: future optional automation hooks (create only when repeated manual tasks justify automation).

## Templates
- `task.md`: lightweight daily work-item template for small features, bugfixes, refactors, and maintenance.
- `feature-spec.md`: implementation-ready feature definition.
- `adr.md`: lightweight architecture decision records.
- `pr-review.md`: standardized review output.
- `test-plan.md`: deterministic test execution and coverage mapping.
- `threat-model.md`: practical security review artifact for auth/data/API-risk changes.

## Examples
- `laravel-project`: backend feature collaboration (architect + backend + laravel + qa + code-review).
- `vue-project`: frontend feature collaboration (architect + frontend + vue + qa).
- `react-project`: frontend feature collaboration (architect + frontend + react + qa).
- `fastapi-project`: API feature collaboration (architect + backend + fastapi + qa + code-review).

## Customization
- Keep role sections intact (`Role`, `Responsibilities`, `Constraints`, etc.).
- Add domain-specific rules under each agent’s `Coding Standards`.
- Extend workflows by adding explicit handoff contracts, not vague instructions.

## Contribution Guidelines
1. Add or update one agent/workflow/template per change.
2. Preserve required section headers for compatibility.
3. Include at least one practical example update when behavior changes.
4. Ensure strong typing, testing, and API contract guidance remain explicit.
