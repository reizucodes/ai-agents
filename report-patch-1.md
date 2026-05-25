# AI Agents Framework — Patch Validation Report (Priority 1 Patch)

**Reviewer:** Claude Sonnet 4.6
**Date:** 2026-05-25
**Scope:** Read-only proofreading and validation. No files were modified.
**Initial Review Reference:** [`report.md`](report.md) — first-touch runtime onboarding review.

## Context

`report.md` documented the initial runtime onboarding review of the `ai-agents/` framework. Codex reviewed that report and applied a narrow Priority 1 patch targeting runtime-facing files only.

Files modified by Codex:
- `INDEX.md`
- `AGENTS.md`
- `.ai/policies/runtime-safety.md`
- `.ai/workflows/bugfix.md`
- `.ai/workflows/feature.md`
- `.ai/workflows/refactor.md`
- `.ai/workflows/release.md`

Codex claimed fixes:
1. Runtime entry sequence clarity
2. `stack agent` definition
3. Workflow entry-point mapping
4. Decision Gate policy discoverability
5. `AGENTS.md` reference to `.ai/policies/decision-gates.md`
6. Workflow references to `.ai/policies/decision-gates.md`
7. Runtime safety / approval-level alignment
8. Missing Security handoff in `bugfix.md`

Files reviewed (per task scope):
- `INDEX.md`
- `AGENTS.md`
- `.ai/policies/runtime-safety.md`
- `.ai/policies/approval-levels.md`
- `.ai/policies/decision-gates.md`
- `.ai/workflows/bugfix.md`
- `.ai/workflows/feature.md`
- `.ai/workflows/refactor.md`
- `.ai/workflows/release.md`

Ignored per task scope: `docs/*`, formatting preferences, broad redesign ideas, new runtime-specific files, new agents/workflows/hooks/skills.

---

## Executive Summary

Codex's patch is well-targeted and addresses six of its eight claimed fixes cleanly. Two issues are partial or unresolved: the approval-levels / runtime-safety alignment improved but remains logically incomplete, and three pre-existing Critical issues from `report.md` (CLAUDE.md, codex.session, docs/ link) are outside the stated patch scope and remain open. No regressions were introduced. The patch is safe to accept with documented follow-ups.

---

## Patch Validation

### 1. Runtime entry sequence clarity
**Pass**

INDEX.md now opens with a new `## Runtime Entry` section:
> 1. Read `AGENTS.md` for global runtime instructions and governance baseline.
> 2. Use this file (`INDEX.md`) to select risk path, templates, workflows, and participating agents.

This directly resolves the finding in `report.md` that a runtime starting at INDEX.md would never be directed to AGENTS.md. The placement at the very top of the file ensures it is encountered before any matrix or flow content.

---

### 2. `stack agent` definition
**Pass**

The line immediately following the Quick Start Matrix now reads:
> `Stack agent` means one or more technology agents from `.ai/agents/`: `laravel`, `vue`, `react`, `node-express`, `python`, `fastapi`.

This resolves the original ambiguity documented in `report.md`. "One or more" also implicitly acknowledges multi-stack scenarios, which was flagged as a secondary concern. The definition is placed at the first point of use.

---

### 3. Workflow entry-point mapping
**Pass**

A new `Workflow entry points:` block appears in the Recommended Flows section:
```
- Choose `bugfix.md` for defects/regressions.
- Choose `feature.md` for net-new behavior.
- Choose `refactor.md` for internal structural improvement without intended behavior change.
- Choose `release.md` for deployment/release readiness work.
```
The descriptions are mutually exclusive and unambiguous. A runtime can now select the correct workflow file without inference.

---

### 4. Decision Gate policy discoverability
**Pass**

Two additions in INDEX.md:
1. In the Workflow Automation Model body: `Decision behavior is defined in .ai/policies/decision-gates.md.`
2. A new `## Policy Map` section listing all seven policy files with one-line descriptions.

Every policy is now discoverable from the primary entry point. The Policy Map is the clearest improvement in the patch — a runtime can identify all governance files in a single read without traversing the directory.

---

### 5. AGENTS.md reference to `.ai/policies/decision-gates.md`
**Pass**

The Governance Policy References list in AGENTS.md now includes `.ai/policies/decision-gates.md`. It was absent before. The reference is correctly inserted between `approval-levels.md` and `runtime-safety.md`. All seven policies are now referenced in both AGENTS.md and INDEX.md.

---

### 6. Workflow references to `.ai/policies/decision-gates.md`
**Pass**

All four workflow files now include this line in their Stage Progression Rules sections:
> `Decision Gate behavior must follow .ai/policies/decision-gates.md.`

This is consistent across bugfix.md, feature.md, refactor.md, and release.md. The placement within Stage Progression Rules is correct — it appears immediately after the Approval Decisions rule, making the two-policy relationship explicit at the point of use.

---

### 7. Runtime safety / approval-level alignment
**Partial**

Three changes were made to runtime-safety.md:

**Resolved:** Branch deletion moved to explicit Level 2. This now matches approval-levels.md.

**Resolved:** A new Rule 5 added: `If action-level classification is ambiguous, use .ai/policies/approval-levels.md as the source of truth.` This is a reasonable precedence mechanism.

**Still unresolved — two directions of misalignment remain:**

First: `git add` is in approval-levels.md Level 1 but still absent from runtime-safety.md Level 1. A runtime reading only runtime-safety.md sees `git add` as unclassified, defaulting to Level 0 (allowed). The precedence rule helps only if the runtime recognizes the ambiguity — but from runtime-safety.md's perspective, `git add` is not marked ambiguous, it simply is not listed. A runtime has no signal to check approval-levels.md.

Second: `git merge` and `git rebase` appear in runtime-safety.md Level 1 but are absent from approval-levels.md Level 1. Since the patch designates approval-levels.md as the source of truth, these two operations now have no Level 1 classification in the designated authoritative file. A runtime applying the precedence rule would find them missing from the source of truth and have no classification to apply.

The precedence rule solves the `git add` ambiguity only if a runtime proactively consults both files. It does not resolve the inverse problem where runtime-safety.md lists operations that approval-levels.md does not.

---

### 8. Missing Security handoff in `bugfix.md`
**Pass**

The Security conditional handoff now appears in both relevant sections:

**Agent Handoffs:**
> Security -> Stack/QA (conditional): security findings, risk impact, required validation checks.

**Handoff Requirements:**
> Security -> Stack/QA (conditional): risk findings, required mitigations, validation scope.

Both entries are correctly marked conditional, matching the High Risk Path requirement. The handoff is consistent with how Security handoffs are expressed in feature.md.

---

## Regressions Introduced

None identified.

All changes are strictly additive. No existing content was removed, inverted, or contradicted by the patch. The new Rule 5 in runtime-safety.md explicitly defers to approval-levels.md rather than asserting a conflicting rule, which is safe. The "one or more" addition to the stack agent definition is accurate and introduces no new constraints.

---

## Remaining Priority 1 Issues

These issues still materially affect runtime onboarding and were not addressed by the patch.

**1. `git merge` and `git rebase` absent from approval-levels.md Level 1**

The patch designated approval-levels.md as the source of truth for action-level classification. However, approval-levels.md Level 1 does not list `git merge` or `git rebase`. A runtime applying the precedence rule now finds these operations unclassified in the authoritative file. This is the inverse of the original `git add` problem and is a direct consequence of the precedence rule without harmonizing the lists.

**2. `codex.session` still present at the repository root**

The file `codex resume 019e5cc3-3e8f-7203-94a5-181793aa9b53` remains untracked and unignored. It was flagged as Critical (C3) in `report.md`. A Codex runtime encountering this file at startup may attempt session resume behavior rather than fresh onboarding. This was not in Codex's stated fix list and remains a live hazard.

**3. No CLAUDE.md exists**

Claude Code does not auto-load AGENTS.md. Without a CLAUDE.md, no framework instructions are applied for Claude Code users unless explicitly prompted. This was Critical (C1) in `report.md` and was not addressed. The patch's runtime entry sequence improvements are invisible to Claude Code unless the user manually requests AGENTS.md be read.

**4. No link from INDEX.md to `docs/`**

The patched INDEX.md adds a Policy Map and workflow entry-point block but still does not reference the `docs/` directory. The prompting guide, use-cases, and runtime compatibility documentation remain undiscoverable from the entry path. This was Critical (C4) in `report.md`. Noted here because the fix requires a change to INDEX.md, which is a patched file.

---

## Safe Follow-Up Improvements

These are not blockers but should be addressed in the next pass.

**Harmonize the approval action lists.** Add `git merge` and `git rebase` to approval-levels.md Level 1 to make the authoritative file complete. Separately, consider whether `git add` should be added explicitly to runtime-safety.md Level 1 or whether the precedence rule is considered sufficient coverage.

**Add Security engagement criteria to refactor.md.** feature.md and bugfix.md both contain a structured "Include security review when any of the following apply" block. refactor.md does not. Its High Risk Path says security is optional "when sensitive/auth/public API surfaces are touched" but provides no enumerated trigger list. A brief block matching the other workflows would eliminate the inconsistency.

**Add ideation as a conditional Step 0 in feature.md's Execution Order.** INDEX.md's Workflow Automation Model includes ideation before product-spec, but feature.md's Execution Order begins at product-spec. A runtime using feature.md for a vague prompt would skip ideation. A parenthetical like "(Step 0: ideation when prompt is vague or direction is undefined)" would close the gap without restructuring the file.

**Add `examples/` pointer context to the Examples entry in INDEX.md.** The new `## Examples` section reads: `Use examples/*/README.md for scenario references that show how agents collaborate by stack.` Noting that examples illustrate agent collaboration patterns rather than complete feature implementations would set correct expectations for a runtime reading them.

---

## Recommendation

**Accept with minor follow-up.**

The patch correctly addresses its six cleanly-passing claims and leaves no regressions. The partial on runtime-safety / approval-levels alignment is a meaningful improvement despite being incomplete — the precedence rule is a defensible design choice that prevents most practical misclassifications. The remaining Priority 1 issues (codex.session, CLAUDE.md, docs/ link, and the `git merge`/`git rebase` gap in approval-levels.md) should be queued for the next narrow patch rather than blocking acceptance of this one.
