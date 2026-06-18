# Laravel Example: Team Assignment Feature

## Scenario
Add `POST /api/v1/projects/{project}/members` to assign users to projects with role validation and audit logging.

## Role Collaboration (5-Phase Flow)

1. **Planning Council** (`project-owner`, `project-manager`, `dev-team-lead`, `cybersecurity-analyst`)
   - `project-owner` confirms scope and risk acceptance.
   - `dev-team-lead` defines the boundary: `ProjectMembershipService` owns assignment rules.
   - Contract: request `{user_id, role}`; response `{id, project_id, user_id, role, assigned_at}`.
   - Risks: duplicate membership, privilege escalation. `cybersecurity-analyst` flags authorization concerns.
2. **Implementation** (`backend-developer` inheriting `.ai/agents/personas/laravel.md`, `database-administrator`)
   - Uses Form Request for validation and Policy for authorization.
   - Implements service + transactional write + event dispatch (`ProjectMemberAssigned`).
   - Returns API Resource; `database-administrator` adds index guidance for `project_user(project_id, user_id)`.
3. **QA** (`qa-specialist`, `qa-team-lead`)
   - `qa-specialist` tests success, invalid role, duplicate assignment, unauthorized caller.
   - Adds regression test for existing membership idempotency.
   - `qa-team-lead` consolidates findings and signs off the Quality Gate.
4. **Business Go/No-Go** (`project-owner`, `project-manager`)
   - Confirms acceptance criteria met and release readiness.
5. **Commit / PR** (`pr-manager`, `documentation-writer`)
   - `pr-manager` produces the commit message, PR body, and change summary.
   - `documentation-writer` updates OpenAPI and the run report.

## Practical Deliverables
- `feature-spec.md` with API and migration notes.
- `technical-design.md` if schema changes warrant it (per artifact matrix).
- PHPUnit feature tests + unit test for assignment rules.
- OpenAPI update for endpoint and error responses.
- PR description by `pr-manager`.
