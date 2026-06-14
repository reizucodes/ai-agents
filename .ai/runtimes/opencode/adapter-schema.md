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
- `.ai/agents/*` is canonical.
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

Required pattern:
- "Before performing role work, read and follow the canonical role contract"
- "This adapter is not the source of truth"
- "If this adapter conflicts with the canonical role contract, follow the canonical role contract"

## Generation Scope Rule
Default generated OpenCode adapter set mirrors established Claude/Codex orchestration-level generation strategy:
- `project-manager`
- `product-spec`
- `architect`
- `backend`
- `frontend`
- `tester`
- `reviewer`
- `documentation`

Optional (opt-in by explicit request/policy):
- `security`
- `devops`
- `database` (only if canonical role exists)

Do not generate runtime-native framework specialist adapters unless the canonical runtime strategy is explicitly changed.

## Validation Rules
1. file path under `.opencode/agents/`
2. first line is `---` (frontmatter starts at byte 0)
3. metadata block present with all required keys
4. metadata block appears after frontmatter (never before frontmatter)
5. valid timestamp and fingerprint format
6. canonical source file exists
7. frontmatter includes `description` and `mode`
8. body includes canonical-first required statements
9. no full canonical-role duplication
10. names map to canonical roles only
