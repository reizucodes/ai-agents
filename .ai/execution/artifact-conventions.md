# Artifact Conventions Contract

## Purpose
Define lightweight, runtime-agnostic artifact conventions for planning, architecture, testing, and review outputs.

## Definitions
- Code-changing run:
  - Any run that changes repository files, including source code, tests, configs, docs, workflow contracts, or generated artifacts.
  - Pure Q&A, explanation-only, search-only, and planning-only runs with no repository file changes are not code-changing runs.
- Audit report artifact:
  - Required per code-changing run at `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
  - Must include:
    - task summary,
    - run type,
    - classification,
    - execution mode,
    - parent-session work performed (orchestration/preflight/merge/final validation),
    - delegated child agents actually invoked,
    - files changed,
    - tests run,
    - artifacts produced,
    - final result/status,
    - unresolved risks/blockers or skipped validations,
    - whether `docs` produced the report or parent/main produced it.
  - Participation accuracy rules:
    - do not list an agent as used/completed unless it was actually spawned/invoked,
    - if delegated mode was selected but child spawn failed, disclose fallback and continue with accurate participation.
  - Runtime trace exports/updates are separate artifacts and must not replace this run report.

## Filename Convention
- New artifact filenames must use timestamp-first naming for chronological sortability and run traceability:
  - `YYYYMMDD-HHMMSS-<artifact-name>.<ext>`
- Timestamp requirements:
  - filename-safe numeric UTC/local runtime timestamp with no separators beyond the single `-` between date and time,
  - generated at artifact creation time,
  - placed before the descriptive artifact name.
- This applies to artifacts under `specs`, `architecture`, `tests`, `reviews`, `docs`, and `reports`, including remediation and final-rerun artifacts.
- Historical artifacts do not require renaming.
- Rationale:
  - improves auditability by making creation order explicit,
  - improves chronological trace readability across repeated runs,
  - clarifies remediation/final-rerun sequencing without relying on content inspection.
- Examples:
  - `/artifacts/specs/20260528-221530-backend-refactor-phase-0-spec.md`
  - `/artifacts/architecture/20260528-221545-backend-refactor-phase-0-architecture.md`
  - `/artifacts/tests/20260528-221610-backend-refactor-phase-0-test-report.md`
  - `/artifacts/reviews/20260528-221630-backend-refactor-phase-0-review.md`
  - `/artifacts/docs/20260528-221700-run-report.md`
  - `/artifacts/reports/20260528-221715-release-readiness-report.md`
  - `/artifacts/reviews/20260528-223000-backend-refactor-phase-0-remediation-review.md`
  - `/artifacts/docs/20260528-223100-final-rerun-run-report.md`

## Canonical Rule
- `.ai/*` remains canonical for process and role contracts.
- Artifacts are versionable execution outputs stored in the repository.
- Diagram artifacts are outputs, not new agent types.

## Artifact Directories
- `/artifacts/specs/`
  - Product-spec artifacts, consolidated specs, and specification diagrams.
- `/artifacts/architecture/`
  - Architect handoff artifacts and technical diagrams.
- `/artifacts/tests/`
  - Tester outputs, test plans, traceability, and validation evidence.
- `/artifacts/reviews/`
  - Reviewer findings, approvals, and remediation summaries.
- `/artifacts/docs/`
  - Docs run reports, parent fallback audit reports, and documentation handoff summaries per run (initial/remediation/final-rerun, including non-merge-ready outcomes).

Directory creation rule:
- Artifact directories are expected outputs and should be created on demand when a phase runs and the directory is missing.
- For docs-run artifacts, when `/artifacts/docs/` is missing but sibling phase folders exist, infer `/artifacts/` as the artifact root and write to `/artifacts/docs/`.
- For any code-changing run, writing `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md` is mandatory even when `docs` is not invoked.

## Diagram Source Format
- Mermaid source is canonical for diagrams.
- Preferred formats:
  - `.mmd` files
  - Markdown files containing Mermaid code blocks
- PNG/SVG rendering is optional and must not be required.

## Diagram Generation Rules
- Generate diagrams for Medium/Large work when they improve clarity.
- Generate diagrams for:
  - async flows
  - payment/auth/order systems
  - multi-service interactions
  - stateful workflows
- Skip diagrams for:
  - Tiny tasks
  - isolated bugfixes
  - straightforward CRUD changes

## Phase Ownership
- `project-manager`: optional high-level planning/workflow diagrams for Medium/Large only.
- `product-spec`: user/business/state/edge-case flow diagrams as specification artifacts.
- `architect`: technical/system/sequence/component/integration diagrams as implementation handoff artifacts.
- `docs`: run-specific docs report artifacts plus documentation handoff status.
- Executors consume approved diagrams/specs; they do not redefine business/process flows.
