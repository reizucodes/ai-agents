# FastAPI Example: API Key Management Endpoints

## Python Tooling Standard

### Project Assumptions

- `pyproject.toml` is required.
- `uv.lock` is required and must be committed to version control.
- `.venv/` is local-only and should not be committed.

### New Project

```bash
uv init
uv add fastapi uvicorn
uv add --dev pytest ruff
```

### Existing Project Setup

```bash
uv venv
uv sync
```

Optional activation:

```bash
# macOS/Linux
source .venv/bin/activate

# Windows PowerShell
.venv\Scripts\Activate.ps1
```

Use `uv run <command>` for normal execution so manual activation is optional.

### Run Application

```bash
uv run fastapi dev app/main.py
```

Alternative:

```bash
uv run uvicorn app.main:app --reload
```

### Run Tests

```bash
uv run pytest
```

### Lint and Format

```bash
uv run ruff check .
uv run ruff format .
```

### Run Scripts

```bash
uv run python script.py
```

`pip` + `requirements.txt` workflows are not allowed for FastAPI projects in this repository.

## Scenario
Implement `/api/v1/api-keys` create / list / revoke endpoints with strict response schemas, scoped auth, and auditability.

## Role Collaboration (5-Phase Flow)

1. **Planning Council** (`project-owner`, `project-manager`, `dev-team-lead`, `cybersecurity-analyst`)
   - `dev-team-lead` defines domain boundaries: key lifecycle service, audit service, auth boundary.
   - Specifies versioned contract and non-breaking error schema.
   - `cybersecurity-analyst` drives the `threat-model.md` for the scoped-auth surface.
2. **Implementation** (`backend-developer` inheriting `.ai/agents/personas/fastapi.md`, `database-administrator`)
   - Uses APIRouter per domain and Pydantic v2 request / response models.
   - Applies `Depends` for auth scope checks and DB session injection.
   - `database-administrator` adds SQLAlchemy persistence + Alembic migration for key metadata.
   - Ensures consistent envelope: `{data, error, meta}`.
3. **QA** (`qa-specialist`, `qa-team-lead`)
   - Tests create / list / revoke success, unauthorized scope, malformed payload, revoked-key behavior.
   - Adds backward compatibility contract tests from OpenAPI snapshots.
   - `qa-team-lead` signs off the Quality Gate.
4. **Business Go/No-Go** (`project-owner`, `project-manager`)
   - Confirms risk acceptance for the auth surface and release readiness.
5. **Commit / PR** (`pr-manager`, `documentation-writer`)
   - `pr-manager` produces the PR with deterministic error codes and pagination metadata called out.
   - `documentation-writer` updates the OpenAPI snapshot and run report.

## Practical Deliverables
- OpenAPI-first spec with auth and error models.
- `threat-model.md` for the scoped-auth surface (security-sensitive artifact).
- `technical-design.md` covering the schema and migration plan.
- pytest suite (API + service layer + regression contracts).
- Release note: migration order and rollback procedure.
- PR description by `pr-manager`.
