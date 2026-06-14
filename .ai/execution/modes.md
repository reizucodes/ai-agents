# Execution Modes Contract

## Purpose
Define runtime routing shapes for this instruction framework in a runtime-consumable format.

## Core Rules
- This instruction framework is not a runtime.
- Markdown files do not spawn agents.
- `.ai/agents/*` are canonical role contracts, not executable worker definitions.
- The main session is not an implementation agent.
- When a suitable specialist exists, delegation is required.
- Direct implementation by parent/main when a suitable specialist exists is a delegation regression.
- Execution mode is runtime-facing routing metadata, not the primary framework control surface.
- Runtime labels such as `targeted` and `delegated` describe routing shape after delegation is already required; they do not decide whether delegation should happen.
- Pause/resume behavior for requirement ambiguity is governed by `.ai/policies/decision-gates.md` (Requirement Clarification Gate).
- Code-changing run definition: any run that modifies repository files (source, tests, configs, docs, workflow contracts, or generated artifacts).
- Every code-changing run must persist `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- Pure Q&A, explanation-only, search-only, and planning-only runs with no file changes are not code-changing runs.
- Execution-mode input handling is defined in `.ai/execution/execution-mode-input.md`.

## Follow-Up Routing Rule
Every follow-up task must be reclassified before execution.

- Follow-up tasks inherit current feature context.
- Follow-up tasks do not inherit previous delegation decisions.
- Delegation must be decided from current classification and risk.

## Execution Mode Input Model
Accepted input values:
- `auto` (default)
- `sequential`
- `targeted`
- `delegated`

Input-source preference:
- Prefer runtime/launcher metadata or injected context/header values.
- Prompt-body `Execution mode: ...` is fallback only when runtime metadata is unavailable.

Runtime preflight before `targeted`/`delegated`:
- Check runtime delegation capability gates.
- Check exact required Codex role adapters (`.codex/agents/<role>.toml`) when adapter-based routing is expected.
- Record `runtime_spawn_supported`, `execution_mode_input`, `classification`, `required_roles`, `available_adapters`, `missing_adapters`, `delegation_decision`, and `fallback_action`.
- If unavailable:
  - report subagent limitation explicitly,
  - request user approval before sequential role simulation fallback,
  - do not claim delegation occurred.

## Modes

### Mode A: Sequential
Use when:
- the user explicitly says `no subagent` or `main only`,
- runtime has no reliable subagent support and the user explicitly approves fallback after disclosure,
- or the task is pure non-code-changing analysis/review work.

Flow:
1. Load `AGENTS.md`.
2. Load `INDEX.md`.
3. Load `.ai/execution/task-classification.md` and classify task.
4. Select relevant `.ai/policies/*` and `.ai/workflows/*`.
5. Apply relevant `.ai/agents/*` role contracts serially in one runtime agent.
6. Implement, review, validate, and return output.

Requirements:
- All policy and quality gates still apply.
- No delegated/parallel claims unless runtime subagents were actually spawned.
- For code-changing runs, parent/main may complete directly only when the user explicitly bypasses delegation or explicitly approves disclosed fallback.
- For review-only tasks, parent-only sequential execution is allowed only for pure non-mutating analysis with no artifact output.

### Mode B: Planning-Gated Delegated
Use only when:
- runtime explicitly supports subagents,
- runtime can isolate child context safely,
- parent agent decides delegation from classification + eligibility gates and invokes it explicitly at runtime,
- task decomposition can be assigned to disjoint ownership boundaries,
- classification and eligibility contracts allow delegation.
- parent/main remains strictly orchestrator (no direct implementation by parent/main).

Flow:
1. Parent loads `AGENTS.md`, `INDEX.md`, execution/delegation contracts.
2. Parent reclassifies current task/follow-up using `.ai/execution/task-classification.md`.
3. Parent starts discovery/spec phase:
   - `project-manager` first,
   - `product-spec` second.
4. Parent runs user spec-discussion loop when required by Requirement Clarification Gate.
5. Parent produces a consolidated, implementation-ready spec handoff artifact.
6. Parent runs architecture phase with `architect` using the approved consolidated spec.
7. Parent assigns ownership boundaries from architect handoff.
8. Parent validates required proposal artifacts, presents consolidated proposal review package to user, asks for explicit implementation approval, and waits.
9. Parent explicitly spawns implementation children (`backend`, `frontend`) only after discovery/spec and architecture gates are complete and explicit approval is received.
10. Children execute scoped tasks with mapped role contracts.
11. Parent runs validation/review/documentation sequence:
   - `tester`,
   - `reviewer`,
   - `documentation`.
   - when `documentation` is in scope, it must run last and persist `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md` before parent final validation.
12. Parent merges, validates, and returns final output.

Requirements:
- Matching specialist work is never kept in parent/main when the required worker path is available.
- Full delegated mode is distinct from targeted delegation and may be unnecessary for Tiny/Small simple work even when targeted role spawning is required.
- Child execution/concurrency depends on runtime capability.
- Parent remains accountable for final merge and gate compliance.
- User does not need to explicitly request delegated mode; parent selection is automatic when gating conditions match.
- For review artifact-generating tasks, parent automatically routes to `reviewer` and `documentation` when delegation capability is available.
- For Medium/Large delegated work, implementation must not start before:
  - approved consolidated spec,
  - architecture handoff,
  - required proposal artifacts,
  - consolidated proposal review package using `.ai/templates/proposal-review-package.md`,
  - explicit user approval on proposal package.
- Parent must not collapse specialist roles into itself in delegated mode.
- Delegated mode is not sequential role simulation.
- For remediation and final-rerun flows where documentation is in scope, `documentation` still runs last and writes `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- For failed/non-merge-ready delegated runs, `documentation` still runs last and writes `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md` with blockers and next steps.

### Mode C: Targeted Delegation
Use when:
- classified work requires one or more specialist roles,
- full Medium/Large delegated planning is unnecessary or already complete,
- runtime subagents and required role adapters are available,
- parent/main can keep orchestration, merge, and final validation responsibilities.

Decision behavior:
- Code-changing Tiny/Small frontend work: `targeted_required: true`, required role `frontend` unless a generated framework specialist is explicitly selected.
- Code-changing Tiny/Small backend work: `targeted_required: true`, required role `backend` unless a generated framework specialist is explicitly selected.
- Test-only code changes: `targeted_required: true`, required role `tester`.
- Review artifact-generating work: `targeted_required: true`, required roles `reviewer`, `documentation`.
- Pure review/analysis with no artifact output: `main_runtime_allowed: true`, reviewer optional.
- Tiny/Small targeted implementation does not require `SPEC_APPROVED` or `ARCHITECTURE_READY` unless risk, scope, ambiguity, contract changes, architecture changes, or explicit user request escalates the task into a planning-gated flow.

Flow:
- Parent
- Relevant implementation agents only
- Reviewer if behavior changed
- Documentation if docs/API/setup changed
- Parent final validation
- If `documentation` is skipped for Tiny/Small efficiency, parent/main must write `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- Parent/main remains orchestrator; targeted mode is not direct specialist-role collapse.
- Each delegated specialist must produce scoped output/report when runtime supports subagents.
- If subagents are unavailable, request explicit user approval before simulation fallback and disclose that delegation did not occur.

### Mode D: Autonomous (Future Extension)
- Not implemented in this instruction framework.
- Any future autonomous mode must be explicitly versioned and policy-gated.
- Note: automatic targeted/delegated selection by the parent (within Mode A/B/C contracts) is already supported and is distinct from a separate future autonomous runtime mode.

Review-only routing rule:
- Pure review/analysis only (no file changes): parent/main allowed; `reviewer` optional.
- Review artifact-generating: `reviewer` required; `documentation` required for run report/audit/final report artifacts.
- Review + validation: `reviewer` required; `tester` required when validation/coverage/test interpretation is requested; `documentation` required if artifacts change.
- Review + remediation: route to remediation flow with relevant implementation agents and rerun `tester` -> `reviewer` -> `documentation`.

Follow-up docs rule:
- For remediation runs and final reruns where documentation is in scope, `documentation` still runs last and writes a run-specific report artifact.
- For failed/non-merge-ready runs where documentation ran, preserve a documentation run report artifact that records blockers and remaining documentation gaps.
- For code-changing Tiny/Small follow-ups where documentation does not run, parent/main still writes the required run report artifact with status and residual risks.

Notes:
- Presence of markdown contracts does not create runtime spawning behavior.

Examples:
- CORS fix: backend; reviewer optional; documentation optional.
- Backend endpoint + frontend view: backend + frontend + tester; reviewer; documentation optional/required by change impact.
- Export report format update: documentation or parent only; reviewer optional.

## Mode Selection Priority
1. Prefer delegation-first routing by default.
2. Use task classification before selecting planning depth and delegation.
3. Use targeted delegation automatically for code-changing Tiny/Small or artifact-generating review tasks when required roles and runtime capability are available.
4. Escalate to full delegated mode automatically when capability, required adapters, task fit, and planning gates are all true.
5. Reject full delegated mode when eligibility checks fail, even if targeted mode is still required.

## Scenario Regression Matrix
- Pure Q&A / explanation:
  - no delegation required,
  - no audit report artifact.
- Planning-only prompt:
  - planning agents may be used when useful,
  - no audit report unless repository files are changed.
- Tiny frontend-only code change:
  - `targeted_required`,
  - required role `frontend` unless a generated `vue`/`react` adapter is explicitly selected,
  - targeted validation,
  - required `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- Tiny backend-only code change:
  - `targeted_required`,
  - required role `backend` unless a generated `fastapi`/`laravel`/`node-express` adapter is explicitly selected,
  - targeted validation,
  - required `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- Tiny cross-layer code change:
  - targeted delegation to both `backend` and `frontend` (or mapped specialists),
  - targeted validation,
  - required `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- Documentation-only change:
  - use `documentation`,
  - required `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- Test-only change:
  - `targeted_required`,
  - required role `tester`,
  - required `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- Workflow/framework change:
  - use reviewer/documentation as appropriate,
  - required `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`,
  - regenerate adapters when canonical contracts affecting mappings/instructions change.
- Full-project technical review with artifact output:
  - `reviewer` -> `documentation`,
  - `tester` optional/required when validation or coverage verification is requested,
  - no `backend`/`frontend` unless remediation is requested,
  - required `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- Medium/Large feature:
  - `project-manager` -> `product-spec` -> `architect` -> proposal artifact validation + consolidated review package using `.ai/templates/proposal-review-package.md` + explicit approval -> `backend`/`frontend` -> `tester` -> `reviewer` -> `documentation`,
  - required `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- Prompt-body `Execution mode: targeted`:
  - routing input only,
  - does not automatically spawn child agents,
  - parent still classifies, checks runtime spawn support, checks exact required role adapters, then explicitly invokes children when gates pass.
- Missing required adapter:
  - disclose missing role adapter(s),
  - set `fallback_requires_approval: true` when targeted/delegated was requested or required,
  - request approval before sequential role simulation fallback,
  - never claim delegation unless a child was actually spawned/invoked.
- Remediation pass:
  - start from tester/reviewer blockers,
  - use targeted agents,
  - rerun `tester` -> `reviewer` -> `documentation`,
  - required remediation run report.
- Final rerun:
  - use relevant targeted agent(s),
  - rerun `tester` -> `reviewer` -> `documentation`,
  - required final-rerun report.
- Failed/non-merge-ready delegated run:
  - `documentation` still runs last and captures status, blockers, changed files, tests, and next steps.

## Planning and Diagram Guidance
- Tiny/Small: planning agents are optional; diagrams optional.
- Medium: planning agents expected; diagrams encouraged for workflow/ownership clarity.
- Large: planning agents mandatory; diagrams expected when boundaries/flows are non-trivial.

## Planning Proposal Gate (Mandatory When Planning Is In Scope)
After planning roles complete, parent/orchestrator must stop and may not auto-handoff to code-writing roles.

Parent/orchestrator required sequence:
1. Collect planning outputs.
2. Validate required proposal artifacts exist as repository files.
3. Consolidate proposal review package including:
   - project-manager summary,
   - product-spec summary,
   - architect summary,
   - proposed scope,
   - proposed implementation plan,
   - proposed file/folder changes,
   - UX/content direction when applicable,
   - technical approach,
   - assumptions,
   - risks,
   - open questions,
   - generated artifact paths.
4. Present package to user and ask for explicit approval.
5. Wait for explicit approval before implementation starts.

Template requirement:
- Use `.ai/templates/proposal-review-package.md` for the proposal review package in planning-gated flows.

Invalid approval assumptions:
- planning completion alone,
- no user objection,
- inferred or implicit approval.

See:
- `.ai/execution/task-classification.md`
- `.ai/execution/capability-gates.md`
- `.ai/execution/delegation-eligibility.md`
- `.ai/delegation/*`
