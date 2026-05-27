# Parent Orchestrator Contract

## Purpose
Define parent-agent responsibilities in delegated execution.

## Responsibilities
1. Load governing instructions:
   - `AGENTS.md`
   - `INDEX.md`
   - relevant `.ai/policies/*`
   - relevant `.ai/workflows/*`
   - `.ai/execution/*` and `.ai/delegation/*`
2. Reclassify every follow-up task with `.ai/execution/task-classification.md` before execution.
3. Decide mode using capability/eligibility contracts.
4. For Medium/Large tasks, complete planning outputs before implementation delegation:
   - `project-manager`
   - `product-spec`
   - `architect`
5. Select workflow and role mappings.
6. Assign disjoint ownership boundaries before spawning children.
7. Explicitly spawn/invoke children only when delegated mode is allowed.
8. Use targeted follow-up delegation when appropriate:
   - relevant implementation agents only,
   - `reviewer` when behavior changed,
   - `docs` when docs/API/setup/decision notes changed,
   - parent final validation.
9. Collect child outputs and integrate deterministically.
10. Enforce policy gates, quality checks, and final validation.
11. Produce final answer with clear execution disclosure:
   - whether delegation was used,
   - which child roles were used,
   - what merge/review steps were performed.

## Follow-Up Planning-Agent Reuse Rule
Do not rerun `project-manager`/`product-spec`/`architect` for Tiny/Small follow-ups unless at least one applies:
- scope changes,
- acceptance criteria change,
- architecture changes,
- workflow changes significantly,
- user explicitly asks for re-planning.

## Skip-Delegation Explanation Requirement
If parent handles a small multi-surface follow-up directly, parent must include:
- `Classification: Small multi-surface`
- `Delegation decision: skipped`
- `Reason: <low-risk | tightly coupled | faster parent patch | user did not request full delegation>`

## Parent Prohibitions
- Must not claim delegated execution when none occurred.
- Must not allow children to write outside assigned scopes without reassignment.
- Must not bypass policy/approval/quality gates.
- Must not skip mandatory planning gates for Medium/Large work.

## Accountability
- Parent owns the final merged result.
- Parent owns unresolved conflict handling.
- Parent owns final risk disclosure.
