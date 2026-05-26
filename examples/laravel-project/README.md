# Laravel Example: Team Assignment Feature

## Scenario
Add `POST /api/v1/projects/{project}/members` to assign users to projects with role validation and audit logging.

## Agent Collaboration
1. **architect**
   - Defines boundary: `ProjectMembershipService` owns assignment rules.
   - Contract: request `{user_id, role}`; response `{id, project_id, user_id, role, assigned_at}`.
   - Risk: duplicate membership, privilege escalation.
2. **backend**
   - Coordinates generic backend concerns: API/auth/data boundaries, transaction risk, and integration expectations.
   - Delegates framework execution details to Laravel once stack context is explicit.
3. **laravel**
   - Uses Form Request for validation and Policy for authorization.
   - Implements service + transactional write + event dispatch (`ProjectMemberAssigned`).
   - Returns API Resource; adds index guidance for `project_user(project_id,user_id)`.
4. **qa**
   - Tests success, invalid role, duplicate assignment, unauthorized caller.
   - Adds regression test for existing membership idempotency behavior.
5. **code-review**
   - Flags missing authorization test initially; requests explicit 403 contract assertion.
   - Approves after fix and API error schema alignment.

## Practical Deliverables
- Feature spec with API and migration notes.
- PHPUnit feature tests + unit test for assignment rules.
- OpenAPI update for endpoint and error responses.
