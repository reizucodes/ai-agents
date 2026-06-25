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
- `.ai/agents/runtime/*.md` is the canonical source of runtime adapter contracts (exactly 16 files; see `.ai/execution/adapter-role-mapping.md`).
- `.ai/agents/personas/*.md` are NEVER adapter sources; they are inheritance-only skill docs.
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
- Adapter metadata must point to the source role file at `.ai/agents/runtime/<role>.md`.
- Pointer must be explicit and resolvable.
- Adapter `name` must match the canonical runtime filename (without `.md`).

## Canonical-First `developer_instructions` Pattern
Generated adapters must use this pattern:

```toml
developer_instructions = """
This is a generated Codex adapter.

You are a delegated child agent, not the main session.

Before performing role work, read and follow the canonical role contract:
.ai/agents/runtime/<role>.md

This adapter is not the source of truth.
If this adapter conflicts with the canonical role contract, follow the canonical role contract.

You may execute the work your role and assigned scope require, subject to
.ai/policies/approval-levels.md and other applicable policies.

<short runtime-specific role summary, including delegation contract, ownership boundaries,
deliverables, and (for backend-developer, frontend-developer, web-designer) persona inheritance directive>
"""
```

## Persona Inheritance
For `backend-developer`, `frontend-developer`, and `web-designer`, `developer_instructions` must include a directive that the agent loads `.ai/agents/personas/<stack>.md` on demand when the matching stack is detected:
- `backend-developer`: laravel, fastapi, node-express, python
- `frontend-developer`: react, vue
- `web-designer`: react, vue (when surface renders through a JS framework)

Persona files are never copied into the adapter and never generated as `.codex/agents/*.toml`.

Rules:
- Keep runtime-specific role summary short and scoped.
- Do not duplicate full canonical role contracts.
- Runtime-specific summary must explicitly state that the worker is not the main session.
- Runtime-specific summary must explicitly authorize role-defined work within assigned scope and policy gates.
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
- `developer_instructions` must require the child to read `.ai/agents/runtime/<role>.md` before role work.
- `developer_instructions` must state the child is not the main session.
- `developer_instructions` must state that role-defined work is permitted within assigned scope and policy gates.
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
- When runtime supports a main-session orchestration bootstrap, it should be loaded to expose parent routing behavior.
- Bootstrap guidance is derivative from canonical `.ai/*` and must not override canonical contracts.
- Bootstrap should require exact role adapter preflight rather than checking only whether any `.codex/agents/*.toml` exists.

## Non-goals
- This contract does not define adapter generation workflow steps.
- This contract does not define runtime adapter outputs for non-Codex runtimes.
