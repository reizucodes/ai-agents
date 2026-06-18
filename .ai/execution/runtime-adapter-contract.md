# Runtime Adapter Contract

## Purpose
Define how this instruction framework derives runtime-specific adapter artifacts from canonical role contracts.

## Scope
- This contract applies only to runtime adapter work.
- This contract does not apply to normal feature, bugfix, refactor, review, or documentation execution.

## Canonical Source Rule
- `.ai/agents/runtime/*` is the canonical runtime worker source (16 fixed roles).
- `.ai/agents/personas/*` provide stack inheritance content for runtime workers and are never used as adapter sources directly.
- Runtime adapters are derivative outputs only and must never replace or supersede `.ai/agents/runtime/*`.

## Adapter Output Rule
- Runtime adapters may target runtime-specific formats (for example `.codex/agents/*.toml`) in later workflows.
- Adapter outputs are optional execution-layer artifacts, not canonical instruction-layer assets.
- Adapter generation must preserve runtime agnosticism of the canonical source.

## Content Minimization Rule
- Adapters should contain minimal role summaries and strict task constraints.
- Adapters must include a canonical source pointer back to `.ai/agents/runtime/<role>.md`.
- Full role-contract duplication is disallowed unless a runtime strictly requires it.
- If full duplication is required by a runtime, document the requirement and the duplication rationale.

## Content Policy
Each generated adapter must include:
1. Generated header metadata:
   - generated-by marker (workflow or tool id)
   - generated-at timestamp (UTC)
   - canonical-source path
   - canonical-source fingerprint using SHA-256
   - fingerprint notation: `sha256:<hex-digest>`
2. Runtime-required fields:
   - required schema keys for the target runtime
3. Canonical source pointer:
   - explicit path to `.ai/agents/runtime/<role>.md`
4. Runtime-specific constraints:
   - scoped role behavior
   - boundary/ownership constraints
   - caveats required by the consuming runtime

## Drift Policy
- Source path: `.ai/agents/runtime/<role>.md`
- Source fingerprint: SHA-256 captured in adapter metadata as `sha256:<hex-digest>`
- Regeneration trigger:
  - canonical role contract changes,
  - runtime schema changes,
  - adapter policy changes,
  - explicit user request
- Validation rule:
  - adapter fingerprint must match current canonical source fingerprint,
  - required runtime fields must pass schema checks,
  - canonical source pointer must resolve,
  - stale adapters must be regenerated before use

## Required Behavior for Adapter Tasks
When performing adapter-generation tasks, the consuming runtime must:
1. Load this contract explicitly.
2. Load relevant execution/delegation contracts.
3. Load canonical role contracts from `.ai/agents/runtime/*` (inject `.ai/agents/personas/*` only when task detection requires stack inheritance).
4. Generate or validate adapters according to minimization, drift, and content policies.
5. Write a persistent adapter run report file using the active runtime workflow report template.
6. Use default report output path `.ai/reports/codex-adapter-run-report.md` when the active workflow does not define a runtime-specific override.
   - Example runtime overrides: Claude `.ai/reports/claude-adapter-run-report.md`, Codex `.ai/reports/codex-adapter-run-report.md`.
7. Report generated outputs, validation status, unresolved drift, and report artifact path.

## Rejection Rules
Adapter generation or validation must be rejected when any apply:
- canonical role source path is missing or ambiguous,
- runtime target schema/required fields are unknown,
- runtime-specific output path is not approved for the host project,
- required metadata (timestamp/fingerprint/source pointer) cannot be produced,
- requested generation would overwrite canonical `.ai/agents/runtime/*` or `.ai/agents/personas/*`,
- request attempts to treat adapters as canonical source,
- request attempts to bypass policy or quality gates.

## Runtime Notes
- This contract is runtime-agnostic.
- Runtime-specific schema details (for example optional `nickname_candidates`) belong under `.ai/runtimes/<runtime>/*`.
- Runtime-specific adapter details should be layered on top of this contract without modifying canonical role semantics.
