# Execution Modes Contract

## Purpose
Define execution modes for this instruction framework in a runtime-consumable format.

## Core Rules
- This instruction framework is not a runtime.
- Markdown files do not spawn agents.
- `.ai/agents/*` are canonical role contracts, not executable worker definitions.
- Sequential mode is the default and portable execution path.
- Delegated mode is optional and runtime-dependent; when capability and eligibility gates pass, parent should invoke it automatically based on classification/scope (no user "delegated mode" prompt required).
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
- Check Codex adapter discovery (`.codex/agents/*.toml`) when adapter-based routing is expected.
- If unavailable:
  - report subagent limitation explicitly,
  - request user approval before sequential role simulation fallback,
  - do not claim delegation occurred.

## Modes

### Mode A: Sequential (Default)
Use when:
- runtime has no reliable subagent support,
- task scope is small/tightly coupled,
- traceability is more important than parallelism.

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
- For code-changing Tiny/Small runs in sequential mode, use targeted delegation to relevant implementation role(s) rather than full planning pipeline.
- For review-only tasks, parent-only sequential execution is allowed only for pure non-mutating analysis with no artifact output.

### Mode B: Delegated (Optional)
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
11. Parent runs validation/review/docs sequence:
   - `tester`,
   - `reviewer`,
   - `docs`.
   - when `docs` is in scope, it must run last and persist `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md` before parent final validation.
12. Parent merges, validates, and returns final output.

Requirements:
- Delegated mode is never assumed implicitly.
- Child execution/concurrency depends on runtime capability.
- Parent remains accountable for final merge and gate compliance.
- User does not need to explicitly request delegated mode; parent selection is automatic when gating conditions match.
- For review artifact-generating tasks, parent automatically routes to `reviewer` and `docs` when delegation capability is available.
- For Medium/Large delegated work, implementation must not start before:
  - approved consolidated spec,
  - architecture handoff,
  - required proposal artifacts,
  - consolidated proposal review package using `.ai/templates/proposal-review-package.md`,
  - explicit user approval on proposal package.
- Parent must not collapse specialist roles into itself in delegated mode.
- Delegated mode is not sequential role simulation.
- For remediation and final-rerun flows where docs is in scope, `docs` still runs last and writes `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- For failed/non-merge-ready delegated runs, `docs` still runs last and writes `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md` with blockers and next steps.

### Mode C: Autonomous (Future Extension)
- Not implemented in this instruction framework.
- Any future autonomous mode must be explicitly versioned and policy-gated.
- Note: automatic delegation selection by the parent (within Mode A/B contracts) is already supported and is distinct from a separate future autonomous runtime mode.

## Targeted Follow-Up Delegation Mode
For follow-up tasks that do not require full planning rerun, use targeted delegation:
- Parent
- Relevant implementation agents only
- Reviewer if behavior changed
- Docs if docs/API/setup changed
- Parent final validation
- If `docs` is skipped for Tiny/Small efficiency, parent/main must write `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- Parent/main remains orchestrator; targeted mode is not direct specialist-role collapse.
- Each delegated specialist must produce scoped output/report when runtime supports subagents.
- If subagents are unavailable, request explicit user approval before simulation fallback and disclose that delegation did not occur.

Review-only routing rule:
- Pure review/analysis only (no file changes): parent/main allowed; `reviewer` optional.
- Review artifact-generating: `reviewer` required; `docs` required for run report/audit/final report artifacts.
- Review + validation: `reviewer` required; `tester` required when validation/coverage/test interpretation is requested; `docs` required if artifacts change.
- Review + remediation: route to remediation flow with relevant implementation agents and rerun `tester` -> `reviewer` -> `docs`.

Follow-up docs rule:
- For remediation runs and final reruns where docs is in scope, `docs` still runs last and writes a run-specific report artifact.
- For failed/non-merge-ready runs where docs ran, preserve a docs run report artifact that records blockers and remaining documentation gaps.
- For code-changing Tiny/Small follow-ups where docs does not run, parent/main still writes the required run report artifact with status and residual risks.

Notes:
- Keep sequential mode as the portable default.
- Presence of markdown contracts does not create runtime spawning behavior.

Examples:
- CORS fix: backend; reviewer optional; docs optional.
- Backend endpoint + frontend view: backend + frontend + tester; reviewer; docs optional/required by change impact.
- Export report format update: docs or parent only; reviewer optional.

## Mode Selection Priority
1. Prefer sequential by default.
2. Use task classification before selecting planning depth and delegation.
3. Escalate to delegated automatically when capability and task fit are both true.
4. Reject delegated when eligibility checks fail.

## Scenario Regression Matrix
- Pure Q&A / explanation:
  - no delegation required,
  - no audit report artifact.
- Planning-only prompt:
  - planning agents may be used when useful,
  - no audit report unless repository files are changed.
- Tiny frontend-only code change:
  - targeted delegation to `frontend` and/or relevant specialist,
  - targeted validation,
  - required `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- Tiny backend-only code change:
  - targeted delegation to `backend` and/or relevant specialist,
  - targeted validation,
  - required `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- Tiny cross-layer code change:
  - targeted delegation to both `backend` and `frontend` (or mapped specialists),
  - targeted validation,
  - required `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- Docs-only change:
  - use `docs`,
  - required `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- Test-only change:
  - use `tester`,
  - required `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- Workflow/framework change:
  - use reviewer/docs as appropriate,
  - required `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`,
  - regenerate adapters when canonical contracts affecting mappings/instructions change.
- Full-project technical review with artifact output:
  - `reviewer` -> `docs`,
  - `tester` optional/required when validation or coverage verification is requested,
  - no `backend`/`frontend` unless remediation is requested,
  - required `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- Medium/Large feature:
  - `project-manager` -> `product-spec` -> `architect` -> proposal artifact validation + consolidated review package using `.ai/templates/proposal-review-package.md` + explicit approval -> `backend`/`frontend` -> `tester` -> `reviewer` -> `docs`,
  - required `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- Remediation pass:
  - start from tester/reviewer blockers,
  - use targeted agents,
  - rerun `tester` -> `reviewer` -> `docs`,
  - required remediation run report.
- Final rerun:
  - use relevant targeted agent(s),
  - rerun `tester` -> `reviewer` -> `docs`,
  - required final-rerun report.
- Failed/non-merge-ready delegated run:
  - `docs` still runs last and captures status, blockers, changed files, tests, and next steps.

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
