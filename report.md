# AI Agents Framework — Runtime Onboarding Review

**Reviewer:** Claude Sonnet 4.6 (acting as a new AI runtime)
**Date:** 2026-05-25
**Scope:** Read-only analysis. No files were modified.

---

## Executive Summary

This is a well-conceived, Markdown-based workflow framework for AI-assisted software development. The core architecture — a risk-scaled quick-start matrix, role-specific agent files, governance policies, and structured handoff contracts — is coherent and internally consistent. The framework philosophy (autonomous stage progression, interrupt only on meaningful decisions) is sound and well-expressed.

However, the framework has four systemic problems that would meaningfully slow or break a new AI runtime onboarding itself:

1. No native instruction files for Claude Code, Cursor, Cline, or Roo Code — the framework assumes all runtimes read arbitrary Markdown, which is only true when users explicitly prompt them to.
2. The `docs/` directory (8 valuable pages) is completely invisible from the intended entry path (INDEX.md, AGENTS.md, README.md).
3. A `codex.session` file sits at the root untracked and undocumented.
4. Several cross-component links are missing (feature-spec template vs. product-spec output, pr-review template vs. code-review format, approval-levels vs. runtime-safety overlap).

These are fixable without restructuring the framework. The overall architecture is worth preserving.

---

## Discovery Journey

**Step 1 — INDEX.md:** Clear, immediately useful. The Quick Start Matrix and Recommended Flows communicate the framework's intent within one read. However, INDEX.md does not reference AGENTS.md, does not mention `docs/`, and uses the term "stack agent" without defining it.

**Step 2 — AGENTS.md:** Discovered by filename convention, not linked from INDEX.md. Contains critical global output formatting rules (the 6-section requirement), engineering principles, and governance policy references. Any runtime that skips this file will produce non-compliant outputs.

**Step 3 — .ai/agents/*.md:** Consistent structure across all agents. Decision Gate Behavior sections are particularly clear. The separation between process agents (ideation through devops) and stack agents (laravel through fastapi) is evident from the directory listing, not from any explanatory label in INDEX.md or AGENTS.md.

**Step 4 — .ai/workflows/*.md:** All four workflow files use the same skeleton. Handoff contracts and stage progression rules are well-defined.

**Step 5 — .ai/policies/*.md:** Concise, actionable. approval-levels.md and runtime-safety.md have meaningful overlap that requires reconciliation.

**Step 6 — .ai/templates/*.md:** Well-structured. task.md has an excellent filled example. pr-review.md and test-plan.md have no filled examples.

**Step 7 — docs/:** Discovered only by filesystem browsing. Not reachable from any linked path originating at INDEX.md. Contains eight high-value pages including prompting guide, use-cases, and runtime compatibility notes — all invisible from the intended entry point.

**Step 8 — examples/:** Discovered from README.md's "Examples" section. Each example is a lightweight scenario summary (3–4 sections). No actual agent output artifacts are shown.

**Step 9 — codex.session:** A file at the root containing `codex resume 019e5cc3-3e8f-7203-94a5-181793aa9b53`. Undocumented, not in .gitignore, not referenced anywhere. A runtime that reads all root files will encounter this unexplained artifact.

---

## What Was Easy To Discover

- **INDEX.md as an entry point** — the Quick Start Matrix and risk-scaled flow descriptions communicate the framework's intent immediately and without friction.
- **AGENTS.md as a global root** — the engineering principles, coding standards, output formatting rules, and policy references form a clean, self-contained baseline.
- **Agent file structure** — all agents share consistent sections (Role, Responsibilities, Constraints, Decision Gate Behavior, Expected Output Format, Collaboration Guidelines, Delegation Rules, Gate Validation). No guesswork about what to expect.
- **Decision Gate Policy** — decision-gates.md makes the Auto / Recommendation / Approval distinction concrete and provides the required format for Recommendation Decisions. This is one of the clearest policies in the repository.
- **Policy-agent relationship** — policies are referenced from agents rather than embedded in them. This is the right design.
- **Workflow handoff contracts** — both feature.md and release.md have two detailed handoff tables. The "what passes from whom to whom" is explicit.
- **task.md template** — the completed example is practical and immediately usable.
- **threat-model.md template** — the JWT auth example is thorough and realistic.

---

## What Was Difficult To Discover

- **docs/ directory** — not linked from INDEX.md, AGENTS.md, or the README's primary sections. A runtime following the recommended path (INDEX.md first) would never reach docs/prompting-guide.md, docs/use-cases.md, or docs/runtimes.md.
- **AGENTS.md from INDEX.md** — INDEX.md directs the reader to read itself first but does not say "then read AGENTS.md." The output formatting rules (the global 6-section requirement), which are only in AGENTS.md, are invisible to any runtime that stops at INDEX.md.
- **"Stack agent" definition** — INDEX.md's Quick Start Matrix lists "Stack agent" as a required participant but never defines the term in situ. A new runtime must infer from context or from reading README.md that this refers to one of the six stack-specific agents.
- **Multi-stack feature handling** — when a task spans Vue frontend and Laravel backend, no workflow or policy explains how to select or sequence multiple stack agents. The workflow files treat "stack agent" as singular.
- **Agent authoring guidance** — there is no template or guide for creating a new stack agent. README.md's Customization section gives general principles but no structural checklist. This matters when a project needs Go, Django, or Next.js coverage.
- **Examples are summaries, not artifacts** — examples/laravel-project/README.md describes what each agent does in a scenario but shows no actual agent output. A runtime cannot use these as calibration references.
- **codex.session** — a root-level file that contains `codex resume 019e5cc3-3e8f-7203-94a5-181793aa9b53`. Not documented, not in .gitignore. Could cause a Codex runtime to attempt a session resume rather than fresh onboarding.

---

## Architecture Review

**Strengths:**

The three-layer hierarchy (AGENTS.md global baseline → workflow orchestration → agent role definitions) is clean and avoids circular dependencies. The policies layer is orthogonal — referenced universally rather than embedded per agent, which keeps agent files focused on their role rather than on governance plumbing. The `.ai/skills/` and `.ai/hooks/` extension points are correctly deferred behind a "rule of three" threshold rather than populated prematurely.

Risk-scaled paths (Low / Medium / High) are consistently applied across all four workflow files with matching structural detail at each level.

**Weaknesses:**

No native runtime configuration files exist. The framework's claim of runtime-agnostic compatibility rests entirely on the assumption that the runtime will be manually prompted to read the Markdown files. In practice, Claude Code loads CLAUDE.md, Cursor uses .cursorrules or .cursor/rules/, Cline uses .clinerules, Roo Code uses .roorules or .roo/rules/. None of these files exist.

The `docs/` directory is architecturally disconnected from the core discovery path. It represents a parallel documentation layer that cannot be reached by a runtime following the intended entry sequence.

No version or schema metadata exists in any file. A runtime caching policy or agent files has no signal for detecting staleness.

---

## Agent Review

**Process agents (ideation, product-spec, architect, security, qa, code-review, devops):**

All process agents are well-structured, internally consistent, and have explicit Decision Gate Behavior sections with "Should NOT interrupt" and "Should interrupt" lists. These lists are practical and specific, which is the right level of detail for runtime guidance. Policy references are consistent. Collaboration and Delegation sections correctly express agent boundaries.

**Stack agents (laravel, vue, react, node-express, python, fastapi):**

Generally well-structured, but laravel.md explicitly names five individual policy files while most other stack agents write `.ai/policies/*` generically. This inconsistency is minor but creates a question about whether generic references are intentional or an omission. laravel.md is the most explicitly governed; node-express.md is the least.

**Missing agents:**

No agent files exist for Go, Django, Next.js, Svelte, or any other popular stack. This is acceptable for the current version given the framework's stated audience, but there is no agent authoring template to guide additions. The README.md's Customization section gives general principles but no structural checklist.

**Agent interaction gaps:**

ideation.md's Collaboration Guidelines section does not mention qa, code-review, or devops. These agents are never relevant to ideation, which is correct, but the absence of any mention may cause a runtime to exclude ideation from workflows where early discovery should precede product-spec (the INDEX.md "Discovery" path).

Security trigger conditions are repeated across AGENTS.md, multiple workflow files, and security.md. Any change to those conditions must be updated in all locations.

---

## Workflow Review

**Strengths:**

All four workflow files share the same structural skeleton (Purpose, Participating Agents, Execution Order, Stage Progression Rules, Risk Paths, Handoffs, Gate Selection, Risk Assessment, Approval Requirements, Handoff Requirements, Exit Criteria). This consistency means a runtime that learns to parse one workflow file can parse all four.

Stage Progression Rules are stated explicitly and identically across all four workflows: proceed automatically unless a Decision Gate fires. This is the right instruction.

Exit Criteria are well-defined in each workflow. feature.md and release.md have the most complete handoff requirement tables.

**Gaps:**

There is no `discovery.md` workflow file. INDEX.md presents "Discovery (When Idea Is Still Vague)" as a distinct recommended flow (`idea → ideation → product-spec → architect → stack agent`) but no orchestrating workflow document exists for it. This means ideation and product-spec agents have no workflow-level context for when they are the primary entry point.

feature.md's Execution Order starts at product-spec (step 1) but INDEX.md's Workflow Automation Model includes ideation before product-spec. The disconnect means a runtime reading feature.md would not know to engage ideation for vague prompts.

bugfix.md's handoff section is comparatively sparse. The "Agent Handoffs" block lists only two handoffs (`QA -> Stack`, `Stack -> Code-review`), while feature.md lists seven. The Handoff Requirements section adds slightly more detail but doesn't include the Security conditional handoff even though the High Risk Path requires security.

refactor.md has no explicit Security engagement criteria statement. The High Risk Path mentions security as optional "when sensitive/auth/public API surfaces are touched" but there is no equivalent to the "Include security review when any of the following apply" block that appears in feature.md and bugfix.md.

---

## Policy Review

**Strengths:**

approval-levels.md and runtime-safety.md are complementary and practical. Both include approval request format templates. The Level 0 / Level 1 / Level 2 framework is a clean mental model.

decision-gates.md is the strongest policy in the repository. The required format for Recommendation Decisions (Option A / Option B / Recommended / Please choose) is concrete and implementable by any runtime.

secrets-management.md clearly separates "Never" from "Always" from "AI Assistant Rules," which is the right distinction for agent-specific guidance.

**Gaps:**

approval-levels.md and runtime-safety.md overlap in coverage. Both list Level 1 and Level 2 examples, but with different wording and slightly different action sets. `git add` appears in approval-levels.md Level 1 but not in runtime-safety.md Level 1. `git merge` and `git rebase` appear in runtime-safety.md Level 1 but not in approval-levels.md Level 1. A runtime following both policies simultaneously must reconcile these differences with no guidance on which file takes precedence.

quality-gates.md defines four gates (Architecture, Implementation, Quality, Release) but does not include a mapping to which workflows or risk levels require which gates. That mapping lives in the workflow files only. A runtime reading quality-gates.md in isolation cannot determine when each gate applies.

definition-of-done.md has no signaling format. It lists completion criteria by stack but does not specify how an agent communicates DoD pass or fail status. The qa.md output format includes "Release Recommendation" and code-review.md includes "Approval Status," but DoD itself is silent on how completion is signaled.

risk-classification.md's Critical level references "execution-sensitive actions" in its approval requirement without defining the term. A runtime must cross-reference approval-levels.md to find examples.

---

## Documentation Review

**Strengths:**

docs/README.md provides three distinct reading paths (New Users, Contributors, Maintainers), which is a good practice for a multi-audience framework.

docs/prompting-guide.md is practical and well-organized. The progression from Minimal → Goal-Based → Structured prompts is intuitive.

docs/use-cases.md covers diverse personas (solo developer, freelancer, startup, consultancy, open-source, AI engineering team) and maps each to concrete workflow and agent selections.

**Gaps:**

docs/ is not linked from INDEX.md, AGENTS.md, or README.md in any primary section. A runtime or human following the intended entry path cannot discover it.

docs/agents.md is a derived summary of .ai/agents/*.md with no indication that the source files are authoritative. When agent files are updated, docs/agents.md must be manually synchronized, but no note to this effect exists anywhere.

docs/workflows.md includes a Mermaid flowchart only for the feature workflow. bugfix, refactor, and release have no diagrams.

docs/getting-started.md and INDEX.md disagree on discovery order. getting-started.md says to read docs/README.md first, then INDEX.md. INDEX.md positions itself as the primary entrypoint. These two are inconsistent.

The feature-spec.md template is missing a User Stories section. The product-spec.md output format lists "User Stories and Requirements" as step 3, and the Feature Specification Structure in product-spec.md includes "User Stories." The template a runtime would fill in has no corresponding section.

pr-review.md and test-plan.md have no filled examples, unlike task.md, adr.md, and threat-model.md.

---

## Runtime Compatibility Review

### Codex

Codex natively loads AGENTS.md as its root instruction file. This repository is structurally well-matched. INDEX.md is explicitly called out in docs/runtimes.md as the Codex entry point.

One concern: codex.session at the root contains a `codex resume` command with a session ID. Codex may attempt to resume this session rather than treating the repository as a fresh context. The file is untracked and its presence is unexplained.

**Assessment: Good fit with one unresolved artifact.**

---

### Claude Code

Claude Code auto-loads CLAUDE.md, not AGENTS.md. No CLAUDE.md exists in this repository. Claude Code will not automatically apply any of the framework's root-level behavior guidance (engineering principles, output formatting rules, governance references) unless the user explicitly prompts it to read AGENTS.md.

docs/runtimes.md says Claude Code "reads local instructions and project files" and to "start with INDEX.md" — this description does not acknowledge the CLAUDE.md requirement.

**Assessment: Poor fit. Auto-loading gap means no root instructions are applied without manual user intervention.**

---

### Cursor

Cursor uses .cursorrules or .cursor/rules/ for project-level instruction. Neither exists in this repository. Cursor users must manually direct the assistant to read AGENTS.md and INDEX.md.

**Assessment: Poor fit. No native instruction file.**

---

### Cline

Cline reads .clinerules at the project root for custom instructions. No .clinerules file exists.

**Assessment: Poor fit. No native instruction file.**

---

### Roo Code

Roo Code supports .roorules or .roo/rules/ directory. Neither exists.

**Assessment: Poor fit. No native instruction file.**

---

## Missing Connections

| From | To | Gap |
|---|---|---|
| INDEX.md | docs/ | No link. Docs directory is invisible from entry point. |
| INDEX.md | AGENTS.md | No explicit instruction to read AGENTS.md as a prerequisite. |
| feature.md (Execution Order) | ideation.md | Ideation is present in INDEX.md's discovery flow but absent from feature.md's Execution Order. |
| feature-spec.md template | product-spec.md output | Product-spec output requires a User Stories section; the template has none. |
| pr-review.md template | code-review.md Reporting Format | Code-review's 5-field findings format does not map to pr-review.md's section structure. |
| approval-levels.md | runtime-safety.md | Overlapping action lists with no reconciliation note or precedence rule. |
| risk-classification.md | security agent trigger criteria | Same trigger conditions copied into 4+ locations with no canonical source. |
| docs/agents.md | .ai/agents/*.md | Docs version is a derived summary with no "source of truth" note or link. |
| bugfix.md High Risk Path | security.md handoffs | Security is required in the High Risk Path but the Agent Handoffs section omits a Security conditional handoff. |

---

## Ambiguities

**"Stack agent" at first use** — INDEX.md's Quick Start Matrix lists "Required Agents: Stack agent" without defining the term. A parenthetical at first use would eliminate this.

**"Clearly superior" as an automation trigger** — agents are told to proceed automatically when "one clearly superior solution exists." This is subjective. A conservative runtime may pause more than intended; an aggressive one may skip gates.

**Multi-stack feature handling** — workflows present "stack agent" as a singular choice. When a feature requires changes in both Vue frontend and Laravel backend, there is no guidance on whether to run both stack agents sequentially, simultaneously, or to coordinate them through the architect.

**"Lightweight evidence" for Low Risk gates** — feature.md, bugfix.md, and refactor.md all describe Low Risk gate requirements as "lightweight evidence" or "lightweight validation." The term is not defined anywhere.

**git add Level 1 status** — approval-levels.md lists `git add` as a Level 1 action (approval required). runtime-safety.md's Level 1 list does not include `git add`. A runtime following runtime-safety.md as its primary safety reference would execute `git add` without seeking approval.

**When ideation is optional** — AGENTS.md says "engage product-spec before architecture when requirements are unclear." INDEX.md says ideation is for "when idea is still vague." The boundary between "vague" and "unclear" is undefined.

**Security trigger duplication** — the security engagement conditions are stated in AGENTS.md, feature.md, bugfix.md, release.md, and security.md. Any future change requires updating all five locations. There is no single source of truth.

---

## Adoption Assessment

**Can a new project realistically adopt this framework with minimal onboarding?**

**For Codex users: Yes.** Copy `.ai/`, `AGENTS.md`, and `INDEX.md` into a new project. Start at INDEX.md. The Quick Start Matrix is immediately usable. The framework will work as designed, assuming the codex.session artifact is not present.

**For Claude Code users: No, not without a CLAUDE.md.** The root instructions will not be auto-loaded. The framework's governance, output formatting rules, and engineering principles are all in AGENTS.md, which Claude Code will not read unless explicitly asked. This is a blocking gap for this runtime's user base.

**For Cursor, Cline, and Roo Code users: No, not without manual setup.** Each runtime requires its own native instruction file format that does not exist in this repository. Users must know to explicitly direct their runtime to read AGENTS.md and INDEX.md, which is not documented in any onboarding path.

**For any user discovering docs/:** Unlikely without guidance. The prompting guide, use-cases, and runtime compatibility documentation have no discovery path from INDEX.md. A user who finds docs/ will have a much richer onboarding experience than one who does not.

**For a new stack agent contributor:** Blocked by absence of an agent authoring template. The README's Customization section gives general principles but no structural checklist. A contributor adding a Go or Django agent must reverse-engineer the structure from existing agents.

**Overall:** The framework is adoption-ready for Codex users with minimal onboarding friction. It requires meaningful manual adaptation for Claude Code, Cursor, Cline, and Roo Code. The docs/ discoverability gap and runtime file gaps are the two changes that would have the broadest impact on adoption.

---

## Recommendations

### Critical

**C1 — Add CLAUDE.md for Claude Code auto-loading.**
Create a CLAUDE.md at the root that either imports or reproduces the AGENTS.md content. Claude Code will not auto-load any framework instructions without it. This is the single highest-impact change for the framework's stated runtime compatibility claim.

**C2 — Add native instruction files for Cursor, Cline, and Roo Code.**
Create .cursorrules (or .cursor/rules/), .clinerules, and .roorules (or .roo/rules/) with the same root-level instruction content. Without these files, the framework's claim of broad runtime compatibility is aspirational rather than functional.

**C3 — Remove or gitignore codex.session.**
The file `codex resume 019e5cc3-3e8f-7203-94a5-181793aa9b53` at the root is a stale development artifact. It risks confusing a Codex runtime into attempting session resume behavior rather than fresh onboarding. Add it to .gitignore or remove it.

**C4 — Link docs/ from INDEX.md.**
Add a "Documentation" or "Learn More" section to INDEX.md pointing to docs/README.md. The prompting guide, use-cases, and runtime notes are valuable but completely invisible from the intended entry point.

---

### Important

**I1 — Add User Stories section to feature-spec.md template.**
The product-spec agent's output format (step 3: User Stories and Requirements) and Feature Specification Structure both include User Stories. The template a runtime or user fills in does not. This creates a structural mismatch between agent output and the artifact designed to capture it.

**I2 — Consolidate security trigger criteria into a single source.**
The conditions that trigger security agent engagement (auth, sensitive data, public APIs, file uploads, payments, Medium+ risk) appear in AGENTS.md, feature.md, bugfix.md, release.md, and security.md. Extract them into risk-classification.md or security.md as the canonical definition, then reference from the other locations. The existing skills/README.md "rule of three" supports this extraction — these conditions appear in five places.

**I3 — Reconcile approval-levels.md and runtime-safety.md.**
Add `git add` to runtime-safety.md Level 1, add `git merge` and `git rebase` to approval-levels.md Level 1, and add a cross-reference note in each file. The two policies should agree on which actions require approval.

**I4 — Add a "Discovery" workflow file.**
INDEX.md presents a Discovery flow as a first-class path (`idea → ideation → product-spec → architect → stack agent`) but no .ai/workflows/discovery.md file exists. Adding it would give ideation and product-spec agents a workflow-level orchestration context for when they are the primary entry point.

**I5 — Add ideation to feature.md's Execution Order.**
feature.md's Execution Order starts at product-spec. Add a step 0 for ideation conditional on vague or early-stage prompts, matching INDEX.md's Workflow Automation Model which includes ideation before product-spec.

**I6 — Add an agent authoring guide.**
Create a brief checklist or template in docs/ or README.md for adding a new stack agent. The existing Customization section in README.md is not sufficient for a contributor to know which sections are required, which are optional, and what each must contain.

**I7 — Add filled examples to pr-review.md and test-plan.md.**
task.md, adr.md, and threat-model.md all include filled examples. pr-review.md and test-plan.md do not. A runtime calibrating its output against template examples will produce weaker outputs for these two templates.

---

### Nice To Have

**N1 — Add Mermaid diagrams for bugfix, refactor, and release workflows.**
docs/workflows.md includes a Mermaid flowchart only for feature. Adding consistent diagrams for the other three workflows improves visual scanability and parity.

**N2 — Add version metadata to policy files.**
A simple `Last updated: YYYY-MM-DD` or `Version: 1.x` line in each policy file would give a runtime or contributor a signal for staleness. As the framework scales, policy drift becomes harder to detect without timestamps.

**N3 — Cross-reference docs/agents.md to source agent files.**
Add a note in docs/agents.md stating that .ai/agents/*.md are the authoritative sources and docs/agents.md is a derived summary. Prevents contributor confusion about which to update.

**N4 — Resolve the INDEX.md vs. getting-started.md discovery order disagreement.**
getting-started.md says to read docs/README.md first, then INDEX.md. INDEX.md positions itself as the primary entry. Pick one canonical starting point and align the other to it.

**N5 — Add a definition-of-done signaling format.**
definition-of-done.md lists criteria but doesn't specify how an agent communicates pass/fail. Adding a simple "DoD Status: Pass | Fail | Partial" field to the DoD policy would give downstream agents a consistent signal to consume.

**N6 — Expand examples to show actual agent outputs.**
The current examples show scenario summaries. Replacing or augmenting them with representative agent output artifacts (a filled feature-spec, a threat model output, a QA test matrix) would give runtimes calibration references they can use to self-evaluate output quality.

**N7 — Define "lightweight evidence" for Low Risk gates.**
A one-line definition in quality-gates.md (e.g., "For Low Risk changes, lightweight evidence means self-certification that the Implementation Gate checklist was reviewed, with no formal artifact required") would eliminate the ambiguity that "lightweight" currently introduces.

**N8 — Consider extracting repeated security trigger criteria into a skill.**
Following the "rule of three" already defined in .ai/skills/README.md, the security engagement conditions appear in five locations and qualify for extraction into a shared skill file. This is the most obvious candidate for the skills directory's first real entry.
