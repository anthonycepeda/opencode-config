# Global Agent Rules — Anthony

> Personal rules applied across all OpenCode sessions.
> Not committed to git. Project-specific rules live in the project's AGENTS.md.
> Last updated: April 2026

---

## Identity & Context

- Stack: Python 3.12, FastAPI, Kubernetes, Helm, Docker, uv, Ruff, pytest, Pydantic
- Package manager: **uv exclusively** — never suggest pip, pipenv, or poetry
- Linter/formatter: **Ruff exclusively** — never suggest Black or isort
- Internal registry: Artifactory (`artifactory.cib.echonet`) — always use `--extra-index-url` or `pyproject.toml` index config, not PyPI directly when installing internal packages

---

## Code Conventions

### General

- Functions do one thing. If you need "and" to describe it, split it.
- No commented-out code. Delete it or keep it, never comment it out.
- Prefer explicit over clever.
- Add comments only on non-obvious code — where the why or how isn't clear from reading it.

### Python

- Always use `pydantic.BaseModel` — never `dataclass` or `TypedDict`
- `snake_case` for variables and functions, `PascalCase` for classes
- Boolean variables start with `is_`, `has_`, `should_`
- Business logic functions named `<verb>_<noun>`: `generate_summary`, `ingest_document`
- Config via `pydantic-settings` `BaseSettings` singleton in `config.py` — never `os.environ` directly
- Secrets in `.env` (never committed) — `.gitignore` enforced

### Architecture (FastAPI projects)

- `views.py` = HTTP only. `crud.py` = business logic. `models.py` = schemas. Never mix.
- `utils/` never imports from `api/` or `crud.py`. No circular deps.
- Routes are registered in `app.py`, never defined there.

### Testing

- Every new endpoint or backend feature ships with at least one test — no exceptions
- Fixtures in `conftest.py`, not setup/teardown in test files
- Test file mirrors source: `src/api/public/documents/crud.py` → `tests/unit/documents/test_crud.py`

---

## What NOT to Do

- ❌ Never suggest pip, pipenv, or poetry
- ❌ Never suggest Black or isort — Ruff handles both
- ❌ Never call `os.environ` directly — always go through `config.py`
- ❌ Never define routes in `app.py` — only register routers there
- ❌ Never put business logic in `views.py`
- ❌ Never use `dataclass` or `TypedDict` — always `pydantic.BaseModel`
- ❌ Never push directly to `main` — always branch and PR
- ❌ Never hardcode values that belong in config or env vars

---

## uv Gotchas

- TLS inspection is active — standard TLS verification fails on Artifactory
- **Correct fix:** `allow-insecure-host = ["artifactory.cib.echonet"]` in `pyproject.toml`
- `verify-ssl = false` does NOT suppress `BadSignature` errors
- `UV_NATIVE_TLS=1` works for `uv sync` but NOT for `uv add`
- When in doubt: check <https://docs.astral.sh/uv> before answering uv questions

---

## Workflow

- Branching: `main` (stable), `feature/<slug>`, `fix/<slug>`
- Commits: conventional — `feat:`, `fix:`, `chore:`, `docs:`, `refactor:`
- One logical change per commit
- Always update `AGENTS.md` or `CLAUDE.md` when a new pattern is established or a mistake corrected
