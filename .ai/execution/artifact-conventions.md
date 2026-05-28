# Artifact Conventions Contract

## Purpose
Define lightweight, runtime-agnostic artifact conventions for planning, architecture, testing, and review outputs.

## Definitions
- Code-changing run:
  - Any run that changes repository files, including source code, tests, configs, docs, workflow contracts, or generated artifacts.
  - Pure Q&A, explanation-only, search-only, and planning-only runs with no repository file changes are not code-changing runs.
- Audit report artifact:
  - Required per code-changing run at `/artifacts/docs/<run-id>-run-report.md`.
  - Must include:
    - task summary,
    - run type,
    - classification,
    - execution mode,
    - agents used,
    - files changed,
    - tests run,
    - artifacts produced,
    - final result/status,
    - unresolved risks/blockers or skipped validations,
    - whether `docs` produced the report or parent/main produced it.
  - Runtime trace exports/updates are separate artifacts and must not replace this run report.

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
- For any code-changing run, writing `/artifacts/docs/<run-id>-run-report.md` is mandatory even when `docs` is not invoked.

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
