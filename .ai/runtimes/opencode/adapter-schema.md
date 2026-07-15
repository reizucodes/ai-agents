# OpenCode Adapter Schema Contract

## Purpose
Define OpenCode-specific adapter schema constraints for generated markdown agent adapters derived from canonical role contracts.

## Scope
- Applies only to OpenCode adapter generation/validation tasks.
- Does not apply to normal feature/bugfix/refactor/release execution.

## Output Path
- OpenCode adapter output target: `.opencode/agents/*.md`
- Output path applies to consumer/test projects.
- `ai-agents` framework source repository must not contain generated `.opencode/agents/*` unless explicit unsafe diagnostic override is provided.

## Canonical Source Rule
- `.ai/agents/runtime/*.md` is the canonical source of runtime adapter contracts (exactly 16 files; see `.ai/execution/adapter-role-mapping.md`).
- `.ai/agents/personas/*.md` are NEVER adapter sources; they are inheritance-only skill docs.
- OpenCode adapter files are derivative runtime artifacts only.
- Generated adapters must not replace canonical role contracts.

## Generated Metadata Requirements
Each generated OpenCode adapter must include machine-readable metadata with:
- `generated-by`
- `generated-at` (UTC ISO-8601)
- `canonical-source`
- `canonical-source-fingerprint` in format `sha256:<hex-digest>`
- `schema-contract` (`.ai/runtimes/opencode/adapter-schema.md`)

Preferred representation:
- HTML comment metadata block immediately after frontmatter.

## Required Frontmatter
Each generated adapter must include markdown frontmatter with:
- `description`
- `mode` (`primary` or `subagent`)

Frontmatter position rule:
- Frontmatter must be the first content in the file (first line must be `---`).
- No metadata/comments/content may appear before frontmatter.

## Canonical-First Body Pattern
Generated adapters must include:
1. generated adapter notice
2. canonical role contract path
3. not-source-of-truth statement
4. canonical-conflict rule
5. explicit statement that the worker is a delegated child agent, not the main session
6. explicit instruction to read `.ai/agents/runtime/<role>.md` before performing role work
7. explicit statement that role-defined work is allowed within assigned scope and policy gates

Required pattern:
- "Before performing role work, read and follow the canonical role contract"
- "This adapter is not the source of truth"
- "If this adapter conflicts with the canonical role contract, follow the canonical role contract"
- "You are a delegated child agent, not the main session"
- "You may execute the work your role and assigned scope require, subject to `.ai/policies/approval-levels.md` and other applicable policies"

## Generation Scope Rule
The default generated OpenCode adapter set is the canonical 16-role set defined in `.ai/execution/adapter-role-mapping.md`:
- `backend-developer`
- `cybersecurity-analyst`
- `database-administrator`
- `dev-team-lead`
- `devops-engineer`
- `doc-team-lead`
- `documentation-writer`
- `frontend-developer`
- `junior-project-manager`
- `pr-manager`
- `project-manager` (primary)
- `project-owner`
- `qa-specialist`
- `qa-team-lead`
- `ui-ux-designer`
- `web-designer`

`project-manager` is generated with `mode: primary`. The other 15 are `mode: subagent`.

Do not generate runtime-native framework specialist adapters. Persona files at `.ai/agents/personas/{laravel,fastapi,node-express,python,react,vue}.md` are NEVER generated as `.opencode/agents/*.md`; they are loaded on demand by `backend-developer`, `frontend-developer`, and `web-designer` per the persona inheritance rules.

## Persona Inheritance
For `backend-developer`, `frontend-developer`, and `web-designer`, the adapter body must include a directive that the agent loads `.ai/agents/personas/<stack>.md` on demand:
- `backend-developer`: laravel, fastapi, node-express, python
- `frontend-developer`: react, vue
- `web-designer`: react, vue (when surface renders through a JS framework)

Persona files are never copied into the adapter body and never generated as `.opencode/agents/*.md`.

## Validation Rules
1. exactly 16 outputs under `.opencode/agents/`, names match `.ai/execution/adapter-role-mapping.md`
2. no outputs derived from `.ai/agents/personas/*`
3. file path under `.opencode/agents/`
4. first line is `---` (frontmatter starts at byte 0)
5. metadata block present with all required keys
6. metadata block appears after frontmatter (never before frontmatter)
7. valid timestamp and fingerprint format
8. canonical source file at `.ai/agents/runtime/<name>.md` exists
9. frontmatter includes `description` and `mode`
10. `mode: primary` only on `project-manager`; all others `mode: subagent`
11. body includes canonical-first required statements
12. no full canonical-role duplication
13. persona inheritance directive present for `backend-developer`, `frontend-developer`, `web-designer`
14. names map to canonical runtime roles only
15. body states the child is not the main session
16. body requires reading `.ai/agents/runtime/<role>.md` before role work
17. body states that role-defined work is allowed within assigned scope and policy gates
