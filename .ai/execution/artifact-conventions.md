# Artifact Conventions Contract

## Purpose
Define lightweight, runtime-agnostic artifact conventions for planning, architecture, testing, review, and run-report outputs, and codify when artifacts are required.

## Artifact Requirement Matrix

| Work type | Required artifacts |
|---|---|
| Tiny / Small (non-sensitive) | none beyond conversation outputs |
| Medium feature | `feature-spec.md`, run-report |
| Major feature / major fix / major refactor | `feature-spec.md` + `technical-design.md` + run-report + proposal review package |
| Schema change | `technical-design.md` + migration/rollback notes |
| Security-sensitive | `threat-model.md` + proposal review package |
| Deployment-impacting | `release.md` workflow + run-report |
| PR/release handoff | PR description by `pr-manager` + run-report |

Risk tier → matrix mapping lives in `.ai/policies/risk-classification.md`.

## Definitions
- **Code-changing run**: any run that changes repository files (source, tests, configs, docs, workflow contracts, generated artifacts). Pure Q&A / explanation / search / planning-only runs with no file changes are not code-changing.
- **Run report (audit) artifact**: required when the matrix says "run-report". Path: `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`. Contents:
  - task summary, run type, classification, execution mode,
  - parent-session work (orchestration / preflight / merge / final validation),
  - delegated child agents actually invoked,
  - files changed, tests run, artifacts produced,
  - final result/status, unresolved risks/blockers,
  - whether `documentation-writer` produced the report or parent/main produced it.
- Participation accuracy: do not list an agent as used/completed unless it was actually spawned/invoked.
- Runtime trace exports are separate artifacts and do not replace the run report.

## Filename Convention
Timestamp-first for chronological sortability:
`YYYYMMDD-HHMMSS-<artifact-name>.<ext>`

- Filename-safe numeric timestamp, generated at creation time, placed before the descriptive name.
- Applies under `specs`, `architecture`, `tests`, `reviews`, `docs`, `reports`.
- Historical artifacts are not retroactively renamed.

Examples:
- `/artifacts/specs/20260528-221530-feature-spec.md`
- `/artifacts/architecture/20260528-221545-technical-design.md`
- `/artifacts/tests/20260528-221610-test-report.md`
- `/artifacts/reviews/20260528-221630-review.md`
- `/artifacts/docs/20260528-221700-run-report.md`

## Artifact Directories
- `/artifacts/<workflow-or-task-name>/` — preferred root for planning/proposal artifacts for a specific workflow run.
- `/artifacts/specs/` — feature specs, consolidated specs, spec diagrams.
- `/artifacts/architecture/` — technical-design handoff artifacts, technical diagrams.
- `/artifacts/tests/` — test plans, traceability, validation evidence.
- `/artifacts/reviews/` — review findings, approvals, remediation summaries.
- `/artifacts/docs/` — run reports, parent fallback audit reports, documentation handoff summaries.

Directory creation rule: artifact directories are expected outputs and should be created on demand when a phase runs and the directory is missing. Code-changing runs require the run report even when `documentation-writer` is not invoked.

Proposal-gate rule: chat-only planning summaries do not satisfy proposal artifact requirements when the matrix requires artifacts. Required proposal artifacts must be actual repository files before implementation begins. Parent uses `.ai/templates/proposal-review-package.md` to present artifact paths and consolidated planning output before requesting approval.

## Diagram Source Format
- Mermaid (`.mmd` or fenced Markdown blocks) is canonical.
- PNG/SVG rendering is optional and must not be required.

## Diagram Generation Rules
Generate for Medium/Major when they improve clarity: async flows, payment/auth/order systems, multi-service interactions, stateful workflows.
Skip for Tiny tasks, isolated bugfixes, straightforward CRUD.

## Phase Ownership
- `project-manager` / `project-owner` — optional high-level planning/workflow diagrams for Medium/Major.
- `dev-team-lead` — technical/system/sequence/component/integration diagrams as implementation handoff artifacts.
- `ui-ux-designer` — user/state/flow diagrams as specification artifacts.
- `documentation-writer` / `doc-team-lead` — run-specific docs reports + documentation handoff status.
- Executors consume approved diagrams/specs; they do not redefine business/process flows.

## Preflight Output Template

Before any `targeted` or `delegated` execution, parent/main records the following preflight result (internal record or emitted artifact, per runtime):

```yaml
runtime_spawn_supported: true|false|unknown
execution_mode_input:
  value: auto|sequential|targeted|delegated
  source: runtime_metadata|prompt_body_fallback|default
classification:
  size: tiny|small|medium|major
  code_changing: true|false
  artifact_generating: true|false
required_roles: [<canonical role names>]
available_adapters: [<role names with usable adapters>]
missing_adapters: [<role names with missing/stale adapters>]
delegation_decision:
  main_runtime_allowed: true|false
  targeted_required: true|false
  delegated_allowed: true|false
  fallback_requires_approval: true|false
fallback_action: none|parent_main|sequential_role_simulation_pending_approval|sequential_role_simulation_approved|stop_for_missing_capability
```

Role selection examples:
- Frontend Tiny code change: `required_roles: [frontend-developer]`.
- Backend Tiny code change: `required_roles: [backend-developer]`.
- Test-only code change: `required_roles: [qa-specialist]`.
- Pure review / no artifact: `required_roles: []`, `pr-manager` optional.
- Review artifact: `required_roles: [pr-manager, documentation-writer]`.
- Medium feature implementation after approval: planning roles plus implementation, `qa-specialist`, `pr-manager`, `documentation-writer`.

## Canonical Rule
- `.ai/*` remains canonical for process and role contracts.
- Artifacts are versionable execution outputs stored in the repository.
- Diagram artifacts are outputs, not new agent types.
