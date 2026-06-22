# Codex Orchestration Bootstrap

## Purpose
Runtime-facing orchestration instructions for the Codex main session.

This file is derived from canonical `.ai/*` contracts and exists to make routing behavior explicit in Codex runtime contexts.

## Canonical Rule
- `.ai/*` remains canonical.
- This bootstrap is derivative runtime guidance.
- If this file conflicts with canonical `.ai/*` contracts, follow canonical contracts.

## Pre-Preflight Exception Check
See `AGENTS.md § Pre-Preflight Exceptions`.

## Prompt Routing Contract
See `AGENTS.md § Prompt Routing Contract`.

## Codex-Specific Routing Rules
- For spawned agent display/traces, prefer `<nickname> [<canonical-role>]`.
- Runtime nickname alone is non-authoritative; canonical role suffix is required in display form.
- Before specialist delegation, check:
  - runtime delegation capability,
  - exact required Codex role adapters (`.codex/agents/<role>.toml`).
- Required preflight output:
  - `runtime_spawn_supported`
  - `classification`
  - `required_roles`
  - `available_adapters`
  - `missing_adapters`
  - `delegation_decision`
  - `fallback_action`

## Canonical Runtime Workers
See `AGENTS.md § Orchestration Baseline`.

## Persona Inheritance
See `AGENTS.md § Persona Inheritance`.

## Required Routing (5-Phase Flow)
See `INDEX.md § Recommended Flows`.

## Approval Gates
See `.ai/policies/approval-levels.md`.

## Audit/Docs Enforcement
- Code-changing runs that require artifacts (per `.ai/execution/artifact-conventions.md` and `.ai/policies/risk-classification.md`) must persist `/artifacts/docs/YYYYMMDD-HHMMSS-run-report.md`.
- If `documentation-writer` is skipped for Tiny work, the run report requirement is waived. If a run report is required, it must be written by a delegated agent (`documentation-writer` or `doc-team-lead`) — the main session must not write files.
