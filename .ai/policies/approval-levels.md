# Approval Levels & Decision Gates Policy

## Purpose
Standardize execution boundaries and human-in-the-loop decision points so every agent applies the same approval controls. See `.ai/policies/risk-classification.md` for risk tiers; this file does not restate them.

## Three Gate Types

All agent decisions resolve to one of three gates. No additional implicit gates exist.

`L0` / `L1` / `L2` are canonical short aliases for **Auto** / **Recommendation** / **Approval** respectively. Either form may appear in policy, workflow, and runtime documentation; they refer to the same three gates and do not introduce additional gate types.

### Auto (L0)
Runtime proceeds without human input.

- Use when: one clearly superior solution exists, impact is local, and the choice is an implementation detail.
- Examples: typo fixes, formatting, reading code, running read-only commands (tests, linters, search, analysis), naming/structure choices, generating documentation, non-destructive edits.
- Behavior: execute directly; carry outputs into the next stage.

### Recommendation (L1)
Present options, default to the safe path, log the decision.

- Use when: multiple viable solutions exist with tradeoffs, or a state-changing operation needs approval before running.
- Examples: dependency bumps within semver, refactors with tests, choosing between equivalent implementations, JWT vs session auth, RBAC vs ABAC, `git add`/`commit`/`merge`/`rebase`/`stash`/`restore`/`cherry-pick`/`revert`/`tag` create, branch create, dependency install/remove/upgrade, migration execution against shared envs, environment/config changes, file deletion (non-bulk).
- Behavior: pause workflow, present options + recommended path, resume after explicit selection. Record the resolved choice; do not reopen unless new material info appears.

### Approval (L2)
Halt and require explicit user approval (the exact action) before proceeding.

- Use when: action is irreversible, affects shared history, touches production data/availability, or is security-sensitive.
- Examples: deletions of broad scope, schema changes, force-push, `git push`/`push --force`/`reset`/`reset --hard`/`clean -fd`, branch deletion, tag deletion, production deploys, production migrations, secret rotation, credential removal, infrastructure destruction, `DROP TABLE`/`DROP DATABASE`/`TRUNCATE`, security-sensitive changes.
- Behavior: never initiate; require the user to explicitly confirm the exact action. Silence, non-objection, or stage completion is not approval.

## Decision Matrix

| Action Type | Gate | Agent Behavior |
|---|---|---|
| Analysis, docs, tests, planning, read-only ops | Auto (L0) | Execute directly |
| Tradeoff choices, state-changing dev operations | Recommendation (L1) | Present options + request approval before running |
| Irreversible / destructive / prod-critical / secrets | Approval (L2) | Do not initiate; require explicit user command |

## Approval Request Template (L1)
```text
Action: <command or operation>
Reason: <why this is required>
Impact: <files/systems affected>
Rollback: <how to revert safely>
Approval Needed: Yes (L1)
```

## Recommendation Template (L1)
```text
Decision Required
Context: ...

Option A — Pros: ... Cons: ...
Option B — Pros: ... Cons: ...

Recommended: ...

Please choose: 1) ... 2) ...
```

## Explicit Instruction Template (L2)
```text
Requested L2 Action: <operation>
Target Environment: <env>
Risk: <high-level impact>
Confirmation Required: Please explicitly confirm this exact action.
```

## Risky Action Templates

### Runtime Safety Rules
1. If risk is unclear, pause and request clarification.
2. Prefer reversible operations; document rollback path first.
3. Record approval context in workflow artifacts.
4. Escalate High/Critical risk actions to `dev-team-lead` + `devops-engineer` + `pr-manager`.
5. Never claim delegation occurred when no child was spawned.

### State-Changing Git Operations (L1)
- `git add`, `git commit`, `git merge`, `git rebase`, `git cherry-pick`, `git revert`, `git tag` (create), branch create.
- Request approval using the L1 template above before running.

### Irreversible Git/Repo Operations (L2)
- `git push`, `git push --force`, `git reset`, `git reset --hard`, `git clean -fd`, branch deletion, tag deletion.
- Require explicit user instruction using the L2 template.

### Data/Infra Operations
- Migration in non-local/shared env: L1.
- Production deploy, production migration, schema destruction, secret rotation, infra destruction, `DROP`/`TRUNCATE`: L2.

### Decision Resolution Rule
When a user selects an option from a Recommendation gate:
- mark the decision resolved,
- continue automatically,
- treat the selection as authoritative input,
- do not reopen unless new material information emerges or the user requests reconsideration.
