# Decision Gates Policy

## Purpose
Reduce unnecessary user interruption while preserving user control over meaningful product, UX, architecture, scope, security, cost, and complexity decisions.

## Decision Gates

## Auto Decisions
Agent proceeds automatically.

Use when:
- one clearly superior solution exists,
- industry best practice is obvious,
- decision impact is low,
- decision affects implementation details only.

Examples:
- folder structure choices,
- naming conventions,
- TypeScript typing patterns,
- Vue composition patterns,
- component splitting,
- internal refactor structure.

Behavior:
- proceed without user interruption,
- carry outputs directly into the next stage.

Boundary:
- inferable implementation detail -> Auto Decision.

## Recommendation Decisions
Agent pauses and requests user input.

Use when:
- multiple viable solutions exist,
- tradeoffs materially affect outcome,
- product direction changes,
- architecture direction changes,
- scope differs significantly.
- security design choices require human judgment but do not trigger approval-level operations.

Security design choice examples (Recommendation Decisions):
- JWT vs session authentication,
- RBAC vs ABAC,
- local secrets vs cloud secret manager strategy,
- token lifetime strategy,
- public vs private API exposure model.

Required format:
```text
Decision Required
Context:
...

Option A
Pros:
Cons:

Option B
Pros:
Cons:

Recommended:
...

Please choose:
1)
2)
```

Behavior:
- pause workflow until user responds,
- resume automatically after selection.

Boundary:
- tradeoff choice with multiple viable outcomes -> Recommendation Decision.

## Requirement Clarification Gate
Agent pauses and requests targeted clarification when requirements are not sufficiently defined to continue safely.

Trigger:
- missing requirements block safe progress,
- requirements conflict,
- acceptance criteria cannot be made testable,
- multiple materially different product behaviors are valid,
- business/security/compliance rules are undefined.

Behavior:
- pause execution,
- ask targeted clarification,
- identify blocking agent,
- identify blocking reason,
- do not invent requirements,
- do not continue past the blocking ambiguity.

Required format:
```text
Clarification Required
Blocking agent: <agent>
Blocking reason: <reason>
Question:
<single targeted question>

If helpful, include concise options:
1)
2)
```

Resume:
- continue automatically after the user answers,
- re-open the gate only if new material ambiguity appears.

## Proposal Approval Gate
Agent must stop after planning completion and wait for explicit user approval before implementation starts.

Trigger:
- workflow includes planning roles (for example: `project-manager`, `product-spec`, `architect`),
- workflow contract requires proposal artifacts before implementation,
- planning output exists but implementation has not started.

Required pre-approval checks:
- parent/orchestrator collects planning outputs,
- parent/orchestrator verifies required proposal artifact files exist in the repository,
- parent/orchestrator consolidates a proposal review package using `.ai/templates/proposal-review-package.md`,
- parent/orchestrator presents artifact paths and summary package to user.

Approval rules:
- planning completion is not approval,
- lack of objection is not approval,
- inferred intent is not approval,
- implementation starts only after explicit user approval (for example: "approved", "proceed", "continue", "implement", or equivalent explicit instruction).

If artifacts are missing:
- stop workflow,
- report missing artifacts,
- request remediation,
- do not start implementation.

## Approval Decisions
Agent must stop and wait for explicit approval.

Examples:
- git commits,
- git pushes,
- branch deletion,
- dependency upgrades,
- file deletion,
- database migrations,
- production actions,
- security-sensitive operations that map to explicit approval-level actions.

Security operation examples (Approval Decisions):
- secret rotation,
- credential replacement,
- security configuration deletion,
- production security changes,
- production deployments,
- repository modifications that require approval-level review.

Behavior:
- follow `.ai/policies/approval-levels.md`.

Rule:
- security design choices -> Recommendation Decision.
- security-affecting operations requiring approval-level actions -> Approval Decision.
- risky/destructive operation -> Approval Decision.

Decision-type map:
- inferable implementation detail -> Auto Decision.
- tradeoff choice -> Recommendation Decision.
- blocking missing requirement -> Requirement Clarification Gate.
- risky/destructive operation -> Approval Decision.

## Decision Resolution Rule
When a user selects an option from a Recommendation Decision:
- mark the decision as resolved,
- continue workflow progression automatically,
- treat the selected option as authoritative input.

Do not reopen the same decision unless:
- new material information emerges,
- requirements materially change,
- the user explicitly requests reconsideration.
