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

Legacy alternatives such as `pip` + `requirements.txt` may be used only for compatibility with existing non-UV repositories.

## Scenario
Implement `/api/v1/api-keys` create/list/revoke endpoints with strict response schemas, scoped auth, and auditability.

## Agent Collaboration
1. **architect**
   - Defines domain boundaries: key lifecycle service, audit service, auth boundary.
   - Specifies versioned contract and non-breaking error schema.
2. **backend**
   - Coordinates generic backend concerns: API/auth/data boundaries, migration/transaction risk, and integration expectations.
   - Delegates framework execution details to FastAPI once stack context is explicit.
3. **fastapi**
   - Uses APIRouter per domain and Pydantic v2 request/response models.
   - Applies `Depends` for auth scope checks and DB session injection.
   - Adds SQLAlchemy persistence + Alembic migration for key metadata.
   - Ensures consistent envelope: `{data, error, meta}`.
4. **qa**
   - Tests create/list/revoke success, unauthorized scope, malformed payload, revoked-key behavior.
   - Adds backward compatibility contract tests from OpenAPI snapshots.
5. **code-review**
   - Requests explicit pagination metadata and deterministic error codes.
   - Approves after schema and tests are updated.

## Practical Deliverables
- OpenAPI-first spec with auth and error models.
- pytest suite (API + service layer + regression contracts).
- Release note: migration order and rollback procedure.
