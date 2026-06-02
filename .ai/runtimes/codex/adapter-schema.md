# Codex Adapter Schema Contract

## Purpose
Define Codex-specific adapter schema constraints for derivative agent adapters generated from canonical role contracts.

## Scope
- Applies only to runtime adapter tasks in this instruction framework.
- Does not apply to normal feature, bugfix, refactor, review, or documentation tasks.
- Applies to child adapter generation and Codex main-session orchestration exposure.

## Output Path
- Codex adapter output target: `.codex/agents/*.toml`
- Codex orchestration bootstrap target: `.ai/runtimes/codex/orchestration-bootstrap.md`
- This contract defines schema and validation only.
- This contract does not create adapters by itself.

## Canonical Source and Derivative Rule
- `.ai/agents/*` is canonical.
- Codex adapter files are derivative outputs only.
- Generated adapters must not replace canonical role contracts.
- Generated adapters are runtime adapters, not skills.

## Required Fields
Each Codex adapter TOML must include:
- `name`
- `description`
- `developer_instructions`

## Optional Fields
- `nickname_candidates` should be included for mapped adapter roles and must match `.ai/runtimes/codex/nickname-strategy.md`.
- Runtime may ignore `nickname_candidates` and assign display names independently.
- Additional optional runtime fields may be included only when required by the consuming runtime or task context.
- Optional field usage must remain conservative and documented per adapter.

## Generated Header Requirements
Each generated adapter must include machine-readable metadata comments:
- generated-by marker
- generated-at timestamp (UTC)
- canonical source path
- canonical source fingerprint (checksum/hash)
- schema contract reference (`.ai/runtimes/codex/adapter-schema.md`)

## Canonical Source Pointer Requirement
- Adapter metadata must point to the source role file in `.ai/agents/*`.
- Pointer must be explicit and resolvable.

## Canonical-First `developer_instructions` Pattern
Generated adapters must use this pattern:

```toml
developer_instructions = """
This is a generated Codex adapter.

Before performing role work, read and follow the canonical role contract:
.ai/agents/<role>.md

This adapter is not the source of truth.
If this adapter conflicts with the canonical role contract, follow the canonical role contract.

<short runtime-specific role summary here>
"""
```

Rules:
- Keep runtime-specific role summary short and scoped.
- Do not duplicate full canonical role contracts.
- Runtime-specific summary should include display/self-identification rule:
  - use `<nickname> [<canonical-role>]`,
  - if runtime nickname is different, self-identify as `<runtime-nickname-or-configured-nickname> [<canonical-role>]`.

## Drift Metadata Requirement
Adapter metadata must include at least one of:
- canonical source checksum/hash, or
- canonical source version/fingerprint with generated timestamp

Preferred:
- include both checksum/hash and generated timestamp.

## Basic TOML Validation Rules
- File must parse as valid TOML.
- Required fields must exist and be non-empty strings.
- `name` must be lowercase kebab-case and stable.
- `description` must be concise and role-specific.
- `developer_instructions` must follow canonical-first pattern.
- Metadata comments must include required generation/drift markers.

## Name and Nickname Collision Rules
- Adapter `name` must be unique within `.codex/agents/`.
- If target `name` collides with another custom adapter, generation is rejected.
- If target `name` collides with a known built-in runtime agent name, generation should be rejected by default unless explicit override is requested.
- If `nickname_candidates` is provided, candidates should be role-distinct and non-duplicative in the generated set.
- Display labels in generated guidance should always append `[<canonical-role>]`.

## Routing and Reporting Rule
- Use canonical adapter `name` as the authoritative routing and reporting identifier.
- Do not use runtime display nicknames as authoritative identifiers.
- Generated descriptions/instructions should keep canonical role visible and require display format `<nickname> [<canonical-role>]` in traces/logs.

## Invocation Rule
- Presence of `.codex/agents/*.toml` does not trigger delegated execution by itself.
- Codex subagents require explicit runtime invocation by the parent runtime agent.
- Prompt-body `Execution mode: ...` is routing input only; it does not automatically spawn Codex agents.
- When runtime supports a main-session orchestration bootstrap, it should be loaded to expose parent routing behavior.
- Bootstrap guidance is derivative from canonical `.ai/*` and must not override canonical contracts.
- Bootstrap should include execution-mode input handling from `.ai/execution/execution-mode-input.md`.
- Bootstrap should require exact role adapter preflight rather than checking only whether any `.codex/agents/*.toml` exists.

## Non-goals
- This contract does not define adapter generation workflow steps.
- This contract does not define runtime adapter outputs for non-Codex runtimes.
