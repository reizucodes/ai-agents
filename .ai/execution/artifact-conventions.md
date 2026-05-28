# Artifact Conventions Contract

## Purpose
Define lightweight, runtime-agnostic artifact conventions for planning, architecture, testing, and review outputs.

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
- Executors consume approved diagrams/specs; they do not redefine business/process flows.
