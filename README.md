# Reusable AI Agent Library

## Purpose
This library provides reusable, production-oriented workflow agents for coding assistants (Codex, Claude Code, Cursor, Cline, Roo Code). It is designed to be copied into software projects and used immediately for implementation, review, testing, and release workflows.

## Installation
1. Copy `ai-agents/` into your project root.
2. Keep structure unchanged for predictable agent discovery.
3. Point your coding assistant to `ai-agents/AGENTS.md` as the root instruction context.

## Usage
1. Pick the relevant role file in `.ai/agents/`.
2. Run a workflow from `.ai/workflows/` (feature, bugfix, refactor, release).
3. Start from templates in `.ai/templates/` for consistent specs, reviews, and tests.
4. Use `examples/` as reference implementations of multi-agent handoffs.
5. Use `INDEX.md` first for risk-scaled quick-start guidance.

## Workflow Philosophy
Scale process according to risk:
- Fast path for small work.
- Structured path for complex work.
- Security path for sensitive work.

Recommended discovery-to-delivery sequence:
Idea -> Ideation -> Product Spec -> Architect -> Implementation

## Agent Architecture
- `ideation.md`: discovery and concept shaping before product specification.
- `product-spec.md`: requirement quality, acceptance criteria, and implementation-ready scope.
- `architect.md`: system design, boundaries, tradeoffs, risk.
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
- `laravel-project`: backend feature collaboration (architect + laravel + qa + code-review).
- `vue-project`: frontend feature collaboration (architect + vue + qa).
- `react-project`: frontend feature collaboration (architect + react + qa).
- `fastapi-project`: API feature collaboration (architect + fastapi + qa + code-review).

## Customization
- Keep role sections intact (`Role`, `Responsibilities`, `Constraints`, etc.).
- Add domain-specific rules under each agent’s `Coding Standards`.
- Extend workflows by adding explicit handoff contracts, not vague instructions.

## Contribution Guidelines
1. Add or update one agent/workflow/template per change.
2. Preserve required section headers for compatibility.
3. Include at least one practical example update when behavior changes.
4. Ensure strong typing, testing, and API contract guidance remain explicit.
