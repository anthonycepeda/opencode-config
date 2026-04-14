# AGENTS.md

> Living document. Update after every correction, every PR, every pattern discovered.
> Rule: if the agent made a mistake once, add a rule so it never happens again.
> Committed to git — shared with the team.

---

## Project Overview

**Name:** [Project Name]
**Purpose:** [What this does in one sentence]
**Type:** [learning | hybrid | work]
**Stack:** [e.g. Python 3.12 / FastAPI / PostgreSQL / Docker]
**Repo:** [link or path]
**Notes:** [Obsidian vault path, e.g. ~/Library/Mobile Documents/iCloud~md~obsidian/Documents/learning/AI/{project}/]

---

## Architecture

```
project/
  config.py          # pydantic-settings BaseSettings singleton; all env vars + client singletons
  asgi.py            # ASGI entrypoint: create_app(settings)
  api/
    app.py           # create_app(settings) factory: builds FastAPI, registers routers
    public/
      __init__.py    # aggregates all public routers
      {resource}/
        views.py     # HTTP layer only: routing, request/response, exceptions
        models.py    # Pydantic request/response schemas
        crud.py      # business logic
  utils/
tests/
  unit/
  integration/
```

**Key principles:**
- `views.py` = HTTP only. `crud.py` = business logic. `models.py` = schemas. Never mix.
- `utils/` never imports from `api/` or `crud.py`. No circular deps.
- [Add project-specific principles here]

---

## How to Run

```bash
make install    # Install dependencies
make run        # Run locally
make test       # Run tests
make lint       # Lint / format check
make fmt        # Auto-fix lint and format
```

---

## Coding Conventions

### General
- Functions do one thing. If you need "and" to describe it, split it.
- No commented-out code in PRs. Delete it or keep it, never comment it out.
- Prefer explicit over clever.
- Add comments only on non-obvious code — where the why or how isn't clear from reading it.

### Data Modelling
- Always use `pydantic.BaseModel` — never `dataclass` or `TypedDict`
- Applies to all internal models, not just API schemas.

### Naming
- `snake_case` for variables and functions, `PascalCase` for classes
- Boolean variables start with `is_`, `has_`, `should_`
- Business logic functions named `<verb>_<noun>`: `generate_summary`, `ingest_document`

### Error Handling
- Never swallow exceptions silently — always log or raise
- Return structured errors from API, never raw tracebacks
- [Add project-specific error handling rules]

### Testing
- Every new endpoint or backend feature must ship with at least one test — no exceptions for "simple" endpoints
- Use fixtures in `conftest.py`, not setup/teardown in test files
- Test file mirrors source path: `src/api/public/documents/crud.py` → `tests/unit/documents/test_crud.py`

---

## What NOT to Do

- ❌ Don't push directly to `main` — always branch and PR
- ❌ Don't hardcode values that belong in config or env vars
- ❌ Don't call `os.environ` directly — always go through `config.py`
- ❌ Don't define routes in `app.py` — only register routers there
- ❌ Don't put business logic in `views.py` — delegate to `crud.py`
- ❌ Don't use `dataclass` or `TypedDict` — always `pydantic.BaseModel`
- ❌ [Add every pattern the agent got wrong, as you discover them]

---

## Workflow

### Branching
- `main` — stable, deployable
- `feature/<slug>` — new work
- `fix/<slug>` — bug fixes

### PR Checklist
- [ ] Tests pass (`make test`)
- [ ] Linting passes (`make lint`)
- [ ] No debug logs or `print()` statements
- [ ] `docs/` updated if endpoints, config, or architecture changed
- [ ] `README.md` updated if user-facing behavior changed
- [ ] `AGENTS.md` updated if a new pattern was established or a mistake corrected

### Commits
- Conventional commits: `feat:`, `fix:`, `chore:`, `docs:`, `refactor:`
- One logical change per commit — don't bundle unrelated changes

---

## Environment & Config

```bash
# Required
[VAR_NAME]=   # description

# Optional
[VAR_NAME]=   # description — default: [value]
```

Config lives in: `config.py` (pydantic-settings `BaseSettings`)
Secrets managed via: `.env` (never committed — `.gitignore` enforced)

---

## External Services & Integrations

| Service  | Purpose        | Docs   |
|----------|----------------|--------|
| [Name]   | [What it does] | [Link] |

---

## Known Issues / Gotchas

- [e.g. Empty string env var behaves differently from unset — always coerce `"" → None`]
- [Add as you discover them]

---

*Last updated: [date] — [what changed]*
