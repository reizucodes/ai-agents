# Evaluate build-codex-agents as Part of the Execution Layer

## 1. Executive summary
- `build-codex-agents` is valid, but it should be treated as an **execution adapter workflow** driven by canonical role contracts.
- It belongs to **both Layer 2 and Layer 3**:
  - Layer 2: workflow definition (`.ai/workflows/build-codex-agents.md`).
  - Layer 3: output adapters (`.codex/agents/*.toml`) + runtime-specific docs.
- This approach preserves `.ai/agents/*` as source-of-truth and reduces manual duplication, but introduces adapter-staleness risk.
- Do **docs-first** before generating TOML files.

## 2. Official Codex subagents findings
Source of truth: [Subagents – Codex](https://developers.openai.com/codex/subagents)

### 2.1 What `.codex/agents/*.toml` does
- Codex supports custom agent definitions in TOML files under:
  - `~/.codex/agents/` (personal)
  - `.codex/agents/` (project-scoped)
- Each TOML file defines one custom agent and is loaded as a configuration layer for spawned sessions.

### 2.2 Required fields
Per official schema:
- `name` (required)
- `description` (required)
- `developer_instructions` (required)

Optional examples include:
- `nickname_candidates`
- `model`
- `model_reasoning_effort`
- `sandbox_mode`
- `mcp_servers`
- `skills.config`

### 2.3 Runtime behavior and invocation
- Codex can orchestrate subagent workflows and aggregate results.
- **Codex only spawns subagents when explicitly asked.**
- Subagent workflows consume more tokens than single-agent runs.
- Subagents inherit parent sandbox policy; parent runtime overrides can be reapplied to children.
- Global controls remain in config `[agents]`, including:
  - `agents.max_threads`
  - `agents.max_depth`
  - `agents.job_max_runtime_seconds`

### 2.4 Limits/caveats to document
From official page:
- `max_depth` default is `1`; deeper recursion increases cost/latency/resource usage.
- `max_threads` caps concurrent open threads but does not remove recursion costs.
- If custom agent `name` collides with built-in (e.g., `explorer`), custom takes precedence.
- Format may evolve as authoring/sharing mature.

## 3. Architecture alignment analysis

### 3.1 Layer placement
- `build-codex-agents` is:
  - a **Layer 2 workflow** (how adapter generation is performed), and
  - a **Layer 3 adapter mechanism** (Codex-specific execution artifacts).

### 3.2 Workflow vs generator
- It should be both:
  - declarative workflow contract in `.ai/workflows/`;
  - optionally script-backed generator later for deterministic output.

### 3.3 Canonical source preservation
- Yes, if rule is enforced: `.ai/agents/*.md` authoritative; `.codex/agents/*.toml` generated derivative.

### 3.4 Duplication reduction
- Yes. Avoids manually maintaining two sets of agent instructions.

### 3.5 Vendor lock-in risk
- Moderate. Mitigated by keeping Codex adapters isolated in Layer 3 and never moving canonical guidance out of `.ai/agents/*`.

## 4. Source-to-adapter mapping review

Proposed mapping:
- `.ai/agents/backend.md` -> `.codex/agents/backend.toml`
- `.ai/agents/frontend.md` -> `.codex/agents/frontend.toml`
- `.ai/agents/qa.md` -> `.codex/agents/tester.toml`
- `.ai/agents/code-review.md` -> `.codex/agents/reviewer.toml`

### 4.1 Is mapping reasonable?
- Yes. It matches common delegated roles and Codex examples (`reviewer` style).

### 4.2 Should every `.ai/agents/*.md` generate TOML?
- No. Generate only roles that are operationally useful as spawned workers.

### 4.3 Generate first
- First set:
  - backend
  - frontend
  - tester (from qa)
  - reviewer (from code-review)
- Optional next:
  - security
  - docs-researcher equivalent (if docs MCP workflow exists)

### 4.4 Exclude initially
- `ideation`, `product-spec`, `architect`, `devops` should generally remain parent/sequential in early rollout because they are cross-cutting coordination roles and may create high context coupling.

### 4.5 Content strategy
- Do **not** duplicate full `.ai/agents/*` verbatim into TOML.
- Recommended adapter style:
  - concise role summary in `developer_instructions`
  - explicit pointer to canonical file path (`.ai/agents/<role>.md`)
  - strict task-scope constraints for spawned child behavior

## 5. Impact on current repo design

### 5.1 `AGENTS.md`
- Add explicit clause: runtime adapters are derivative artifacts; canonical role guidance remains `.ai/agents/*`.

### 5.2 `INDEX.md`
- Add mode routing:
  - sequential default
  - delegated optional (runtime-dependent)
  - adapter generation workflow as maintenance operation

### 5.3 `.ai/agents/*`
- Keep as-is conceptually, but standardize fields useful for adapter generation (Role, Responsibilities, Constraints already present in many files).

### 5.4 `.ai/workflows/*`
- Add future `build-codex-agents` as a maintenance/build workflow.
- Ensure workflow language does not imply automatic spawn.

### 5.5 `docs/runtimes.md`
- Add direct cross-link to a Codex-subagents doc and runtime matrix.

### 5.6 Future `docs/execution-modes.md`
- Must define delegated mode as runtime-enabled and explicit-invocation only.

### 5.7 Future `docs/runtimes/codex-subagents.md`
- Must include:
  - required TOML fields
  - directory locations
  - invocation requirement
  - limits (`max_threads`, `max_depth`, token/cost implications)

### 5.8 Future `docs/delegation/*`
- Add contracts for ownership, merge, and when to reject delegation.

### 5.9 Terminology updates needed before implementation
- Distinguish terms everywhere:
  - role contract
  - workflow
  - runtime adapter
  - spawned subagent
  - delegated vs sequential mode

## 6. Risk analysis + mitigations

1. Risk: generated adapters mistaken as canonical source.
- Mitigation: header in every generated TOML comment + docs rule: `.ai/agents/*` is source-of-truth.

2. Risk: overclaiming multi-agent support.
- Mitigation: explicit wording in README/docs that spawning depends on runtime capability + explicit invocation.

3. Risk: stale `.codex/agents/*.toml`.
- Mitigation: add regeneration workflow and drift check (compare timestamp/hash from source role files).

4. Risk: instruction duplication divergence.
- Mitigation: keep TOML instructions minimal and reference canonical contracts; avoid full copy.

5. Risk: runtime-specific pollution of runtime-agnostic repo.
- Mitigation: isolate under `.codex/` + `docs/runtimes/`; keep Layer 1 unchanged.

6. Risk: Codex schema/docs evolution.
- Mitigation: maintain a versioned adapter spec doc with last-verified date and doc URL.

7. Risk: delegated mode used where sequential is better.
- Mitigation: add explicit rejection criteria (tiny scope, highly coupled changes, low value parallelism).

## 7. Recommended implementation sequence

Recommended order: **C then A then B/E (phased)**

1. `C` — Codex runtime doc first.
- Add `docs/runtimes/codex-subagents.md` with verified schema and caveats.

2. `A` — Execution layer docs.
- Add `docs/execution-modes.md` and `docs/runtime-capability-matrix.md`.

3. `B` — `build-codex-agents` design/workflow doc.
- Define mapping rules, exclusions, and regeneration policy.

4. `E` — Script-backed generator later.
- Implement only after contracts are stable.

5. `D` — Generate/commit TOML adapters after docs/contracts exist.
- Not first.

Why: minimizes incorrect runtime claims and prevents locking into unstable adapter semantics.

## 8. Final verdict
1. Should `build-codex-agents` be part of Execution Contract & Adapter Layer?
- **Yes.** It is the mechanism that materializes runtime adapters.

2. Should it be implemented before broader execution layer docs?
- **No.** Docs and terms must be fixed first.

3. Should it start as docs-only?
- **Yes.** Start with design + policy + mapping rules.

4. Should `.codex/agents/*.toml` be committed now or deferred?
- **Deferred** until execution docs and mapping contract are merged.

5. Should script-backed generator be added now or later?
- **Later** (after docs and initial manual adapter spec are validated).

6. Minimum viable first PR?
- Add:
  - `docs/execution-modes.md`
  - `docs/runtime-capability-matrix.md`
  - `docs/runtimes/codex-subagents.md`
  - README wording updates clarifying non-runtime nature and delegated-mode caveats
- No TOML generation yet.

## 9. Suggested next implementation prompt
```text
Implement docs-only execution layer clarification for this repo.

Scope:
1) Add docs/execution-modes.md defining sequential, delegated, and future autonomous modes.
2) Add docs/runtime-capability-matrix.md with capability-based columns and conservative unknowns.
3) Add docs/runtimes/codex-subagents.md summarizing official Codex subagents behavior:
   - explicit invocation requirement
   - .codex/agents location
   - required TOML fields (name, description, developer_instructions)
   - global [agents] controls and caveats
4) Update README and docs/runtimes.md to state:
   - this repo is not a runtime
   - markdown files do not spawn agents
   - delegated mode is runtime-dependent

Constraints:
- Do not add .codex/agents/*.toml yet.
- Do not add build-codex-agents workflow yet.
- Keep .ai/agents/* as canonical role contracts.
- Keep changes minimal and scoped.
```

## Sources
- Official Codex subagents documentation: https://developers.openai.com/codex/subagents
