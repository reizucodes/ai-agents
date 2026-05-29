# Adapter Drift Validation Contract

## Purpose
Define step-by-step drift and safety validation for generated runtime adapters in this instruction framework.

## Scope
- Applies only to runtime adapter tasks.
- Does not apply to normal feature, bugfix, refactor, review, or documentation tasks.

## Inputs
- Canonical role source file path (for example `.ai/agents/<role>.md`).
- Adapter output path (runtime-specific, for example future `.codex/agents/*.toml`).
- Runtime schema contract from `.ai/runtimes/<runtime>/*` (for example `.ai/runtimes/codex/adapter-schema.md` when applicable).

## Validation Steps
1. Canonical source existence check
- Verify canonical source file exists and is readable.
- If missing: fail `missing_source`.

2. Adapter output existence check
- Verify adapter output file exists when validation is requested.
- If missing: fail `missing_adapter`.

3. Source fingerprint/checksum check
- Compute canonical source fingerprint using SHA-256.
- Adapter metadata fingerprint must use notation `sha256:<hex-digest>`.
- Compare canonical fingerprint against adapter metadata fingerprint.
- If mismatch: fail `stale_adapter`.

4. Generated timestamp check
- Verify adapter contains generated timestamp metadata.
- Validate timestamp format and presence.
- If missing/invalid: fail `stale_adapter`.

5. Required metadata check
- Verify required metadata exists:
  - generated-by marker
  - generated-at timestamp
  - canonical source path
  - canonical source fingerprint in `sha256:<hex-digest>` format
- If any missing: fail `invalid_schema`.

6. Required field check
- Validate runtime-required adapter fields are present and non-empty.
- If missing/empty: fail `invalid_schema`.

7. Canonical source pointer check
- Validate adapter pointer maps to existing canonical source file.
- If unresolved or mismatched: fail `invalid_schema`.

8. Minimal-content / no-full-duplication check
- Validate adapter content remains minimal and role-scoped.
- Detect unsafe full duplication of canonical role contract unless runtime requirement is explicitly documented.
- If unsafe duplication found: fail `unsafe_duplicate_content`.

9. Runtime-specific schema check
- Validate adapter against runtime-specific schema contract.
- If runtime schema is unavailable/undefined: fail `unknown_runtime_schema`.
- If schema validation fails: fail `invalid_schema`.

10. Reserved-name/collision check (when runtime exposes known names)
- Validate adapter name does not collide with known reserved/built-in names unless explicit override is confirmed.
- Validate adapter name uniqueness within target adapter output scope.
- If collision found: fail `invalid_schema`.

## Failure States
- `missing_source`
- `missing_adapter`
- `stale_adapter`
- `invalid_schema`
- `unsafe_duplicate_content`
- `unknown_runtime_schema`

## Required Remediation
For any failure state, one or more of the following actions is required:
1. Regenerate adapter
- Required for `stale_adapter`, `missing_adapter`, and metadata drift.

2. Reject adapter
- Required for unrecoverable schema violations, unresolved source mapping, or unsafe duplication.

3. Require explicit user confirmation
- Required for reserved-name override or high-risk runtime-specific exceptions.

4. Fallback to sequential mode
- Required when adapter validation cannot be completed safely or runtime schema remains unknown.

## Validation Output
Validation must report:
- source path
- adapter path
- validation result (`pass`/`fail`)
- failure state(s)
- remediation action(s)
- unresolved risk notes

## Non-goals
- This contract does not generate adapters.
- This contract does not define runtime adapter workflow sequencing.
