# Ownership Boundaries Contract

## Purpose
Prevent merge conflicts and unclear accountability during delegated execution.

## Ownership Assignment
Before spawning children, parent must define:
- child role,
- file/module ownership scope,
- allowed interfaces to modify,
- forbidden paths.

## Boundary Rules
- Write scopes must be disjoint by default.
- Shared contract files require explicit single-owner assignment.
- Cross-scope edits require parent approval and reassignment.

## Coordination Rules
- Children treat the target codebase as shared and must not revert unrelated edits.
- Parent resolves boundary violations by reassigning or folding task back into sequential mode.

## Minimum Handoff Data
Each child returns:
- changed files,
- contract-impact summary,
- tests executed or pending,
- known risks and assumptions.

## Fallback
If boundaries cannot be defined clearly:
- delegated mode is not allowed,
- execute sequentially.
