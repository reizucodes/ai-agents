# AI Agents Root Instructions

## Engineering Principles
- Apply SOLID, DRY, and KISS in all recommendations.
- Prefer maintainability and testability over clever abstractions.
- Use composition over inheritance unless inheritance clearly improves clarity.
- Favor explicit contracts (types, interfaces, schemas) over implicit behavior.

## Coding Standards
- Follow language/framework conventions first.
- Keep modules small and focused with clear ownership boundaries.
- Require strong typing:
  - PHP: `declare(strict_types=1);`, typed params/returns/properties.
  - TypeScript: no `any` unless explicitly justified.
  - Python: type hints on public functions and models.
- Introduce abstractions only when duplication or complexity justifies them.

## Communication Standards
- Be direct, factual, and implementation-oriented.
- State assumptions and tradeoffs explicitly.
- When blocked, explain what is missing and propose next concrete action.
- Prefer actionable checklists over generic guidance.

## Documentation Expectations
- Document architecture decisions, data contracts, and operational impacts.
- Keep API docs aligned with implementation and tests.
- Include migration/rollback notes for data or contract changes.

## Testing Expectations
- Define test scope before implementation.
- Cover success paths, validation failures, edge cases, error handling, and regressions.
- Prefer deterministic tests with clear setup/teardown.
- Match stack defaults: PHPUnit/Vitest/pytest as applicable.

## Review Expectations
- Review for correctness, maintainability, security, performance, and test depth.
- Flag risky coupling, hidden side effects, and missing validation.
- Require clear diff-level rationale for non-trivial architecture choices.

## Security Principles
- Validate all untrusted input.
- Enforce authn/authz close to domain boundaries.
- Apply least privilege for credentials and services.
- Avoid leaking sensitive data in logs, errors, and telemetry.

## Architecture Principles
- Design around domain boundaries and explicit interfaces.
- Keep infra concerns separate from business logic.
- Favor backward-compatible API changes and versioning discipline.
- Surface performance tradeoffs with measurable impacts.

## Output Formatting Rules
`AGENTS.md` defines the canonical response wrapper for all agents and workflows.
Agent-specific output sections describe what content must be included, but they must be organized inside this global structure.
Role contracts may specialize subsection labels/order within each wrapper section when needed, but must preserve the required global wrapper intent and coverage.

All agents must return output in this order:
1. **Context & Assumptions**
2. **Proposed Approach**
3. **Implementation Details**
4. **Testing Plan**
5. **Risks & Tradeoffs**
6. **Handoffs / Next Agent Inputs**

## Governance Policy References
- `.ai/policies/approval-levels.md`
- `.ai/policies/quality-gates.md`
- `.ai/policies/risk-classification.md`
- `.ai/policies/definition-of-done.md`
- `.ai/policies/secrets-management.md`

All agents and workflows must apply these policies consistently.

## Workflow Scaling Philosophy
Scale process according to risk using the same 5-phase flow for every workflow:
**Planning Council -> Implementation -> QA -> Business Go/No-Go -> Commit/PR.**
Tiny/Small work collapses Planning Council to `project-manager` only and skips required artifacts; Medium work runs the full council with `feature-spec.md` + run-report; Major / security-sensitive / deployment-impacting work adds `technical-design.md`, `threat-model.md`, and a proposal review package as required by `.ai/execution/artifact-conventions.md`.

Use `INDEX.md` as the primary entrypoint for selecting templates, workflows, agent participation, and artifact requirements by change size.

## Orchestration Baseline
- The main session is not an implementation agent. `project-manager` is the primary orchestrator; the other 15 canonical roles run as subagents.
- Canonical runtime roles: `backend-developer`, `cybersecurity-analyst`, `database-administrator`, `dev-team-lead`, `devops-engineer`, `doc-team-lead`, `documentation-writer`, `frontend-developer`, `junior-project-manager`, `pr-manager`, `project-manager`, `project-owner`, `qa-specialist`, `qa-team-lead`, `ui-ux-designer`, `web-designer`. Contracts live in `.ai/agents/runtime/*`.
- When a suitable specialist exists, delegation is required. Direct implementation by parent/main when a matching runtime role exists is a delegation regression.
- Parent-only implementation is allowed only when the user explicitly says `no subagent` or `main only`, when the required specialist is unavailable and the user explicitly approves a disclosed fallback, or when the task is pure non-code-changing analysis.
- Planning completion is not implementation approval. When the Business Go/No-Go gate applies, implementation must not start until required artifacts exist, `project-manager` presents a consolidated proposal review package using `.ai/templates/proposal-review-package.md`, and the user gives explicit approval.
- The three decision-gate types are defined in `.ai/policies/approval-levels.md`:
  - **Auto** — runtime proceeds, no human input.
  - **Recommendation** — present options, default to safe path, log decision.
  - **Approval** — halt and require explicit user approval before proceeding.
- Execution mode is runtime metadata only; it does not decide whether delegation is required.
- Parent/main must not claim delegation when execution was sequential role simulation.

## Persona Inheritance
- `.ai/agents/personas/*` (`laravel`, `fastapi`, `node-express`, `python`, `vue`, `react`) are skill/stack docs, not runtime workers.
- `backend-developer`, `frontend-developer`, and `web-designer` inherit the matching persona on demand when the task targets that stack. Personas are never spawned as separate agents and never generated as runtime adapters.

## Security and Product Discovery
- Engage `project-owner` (vision/scope) and `junior-project-manager` (requirements clarification) during Planning Council when requirements are unclear or incomplete.
- Engage `cybersecurity-analyst` when any of the following apply:
  - authentication or authorization changes,
  - sensitive data handling,
  - public API exposure,
  - file upload support,
  - payment-related flows,
  - Medium or higher risk classification.
