# Documentation Rules

## Required files

Every module must have these two files:

```
[module]/
├── OVERVIEW.md    # flow, inputs/outputs, file structure
└── DETAILS.md     # public API, types, edge cases, test examples
```

Every module must also contain a `core/` submodule for the initial/core code:

```
[module]/
├── OVERVIEW.md
├── DETAILS.md
└── core/              # core logic — the first code created for the module
    ├── __init__.py
    ├── core.py        # main logic
    └── tests/
        ├── __init__.py
        ├── conftest.py
        └── test_core.py
```

The `core/` submodule is where the foundational code lives — models, core classes, main functions. Secondary concerns (adapters, exporters, formatters, etc.) go in sibling directories at the module level.

The project root must always have:

```
OVERVIEW.md        # system map, module list, dependencies, contracts
```

Templates are at:
- `docs/templates/ROOT_OVERVIEW.md`
- `docs/templates/OVERVIEW.md`
- `docs/templates/DETAILS.md`

---

## When to CREATE documentation

| Trigger | Action |
|---|---|
| Creating a new module or package | Create `[module]/OVERVIEW.md` and `[module]/DETAILS.md` from templates |
| First time working on a module that has no docs | Create both files before writing any code |
| Adding a new module to the system | Add it to the root `OVERVIEW.md` modules table |

---

## When to UPDATE documentation

| Trigger | File to update |
|---|---|
| Adding or changing a public function or class | `[module]/DETAILS.md` → Public API section |
| Adding or changing a model / dataclass | `[module]/DETAILS.md` → Models / Types section |
| Changing how the module flow works | `[module]/OVERVIEW.md` → Main flow section |
| Adding a new external dependency | `[module]/OVERVIEW.md` → External dependencies section |
| Discovering an edge case or gotcha | `[module]/DETAILS.md` → Edge cases section |
| Making a non-obvious design decision | `[module]/DETAILS.md` → Design decisions table |
| Adding or removing a module from the system | Root `OVERVIEW.md` → Modules table |
| Adding or changing a type that crosses module boundaries | Root `OVERVIEW.md` → Cross-module contracts section |
| Changing which module imports from which | Root `OVERVIEW.md` → Module dependencies section |
| Adding or changing a test fixture in conftest.py | `[module]/DETAILS.md` → Main fixtures section |

---

## Rules

1. **Documentation is part of the task.** Never close a task that changed code without updating the relevant docs.
2. **Update docs in the same commit as the code.** Do not leave docs for later.
3. **Root OVERVIEW.md is the source of truth for contracts.** If a type crosses module boundaries, it must be documented there — not only in the module.
4. **Do not duplicate.** If something is in DETAILS.md, do not repeat it in OVERVIEW.md. OVERVIEW links to DETAILS.
5. **Keep OVERVIEW.md short enough to read in 30 seconds.** If it grows too long, move content to DETAILS.md.
6. **The "What is NOT in this module" section is mandatory.** It prevents the agent from searching in the wrong place.

---

## Python tooling

Always use **`uv`** instead of `pip` + `venv` for Python projects. `uv` is a fast Python package installer and resolver written in Rust. It manages virtual environments, dependencies, and scripts in one tool.

### uv commands

```bash
# Create virtual environment
uv venv .venv
source .venv/bin/activate        # Linux / macOS
# .venv\Scripts\activate         # Windows

# Install dependencies (from pyproject.toml)
uv sync

# Install a package
uv add requests
uv add requests --dev

# Run a script in the virtual environment
uv run python script.py
uv run pytest

# Install project in editable mode (with extras)
uv pip install -e ".[test]"

# Update all dependencies to latest
uv sync --all-packages

# Remove a package
uv remove requests

# List installed packages
uv pip list

# Pin dependencies
uv lock
```

### Rules
- **Never use `pip install`** directly — always use `uv add` or `uv sync`
- **Never use `python -m venv`** — always use `uv venv`
- **Commit `uv.lock`** — it pins exact versions for reproducible builds
- **Use `uv run`** to execute scripts — ensures the virtual environment is used

### When to add dependencies
- Adding a new library → `uv add <lib>` (updates `pyproject.toml` + `uv.lock`)
- Adding a dev dependency → `uv add <lib> --dev`
- After adding dependencies → run `uv sync` to install them

---

## Testing

Always write tests for new code. Refer to the full testing guide at [`TESTING_GUIDE.md`](../TESTING_GUIDE.md) for detailed instructions on pytest, hypothesis, mutmut, and atheris.

### Quick rules
- **Write tests before or alongside code** — never skip testing
- **Run `pytest`** before committing — all tests must pass
- **Minimum mutation score: 80%** — use `mutmut run` to verify
- **Use property-based tests (hypothesis)** for mathematical/logical properties
- **Use fuzz testing (atheris)** for input validation and edge cases

### Test commands

```bash
# Run all tests (except fuzz)
uv run pytest -v --ignore=<package>/tests/test_fuzz.py

# Run mutation testing
uv run mutmut run

# Run fuzz testing
uv run python <package>/tests/test_fuzz.py -runs=10000
```

### When to use which tool

| Scenario | Tool |
|----------|------|
| Specific cases, types, exceptions | `pytest` |
| Mathematical properties (commutativity, etc.) | `hypothesis` |
| Validate test suite effectiveness | `mutmut` |
| Unexpected inputs, edge cases | `atheris` |

---

## Checklist before finishing any task

Before marking a task as done, verify:

- [ ] Did I add or change a public function? → Updated `DETAILS.md` Public API
- [ ] Did I add or change a model? → Updated `DETAILS.md` Models / Types
- [ ] Did I change the module flow? → Updated `OVERVIEW.md` Main flow
- [ ] Did I add a new module? → Created both docs + added to root `OVERVIEW.md`
- [ ] Did I change a cross-module type? → Updated root `OVERVIEW.md` Contracts
- [ ] Did I change which module depends on which? → Updated root `OVERVIEW.md` Dependencies
- [ ] Did I make a non-obvious design decision? → Added to `DETAILS.md` Design decisions
- [ ] Did I add/modify Python dependencies? → Used `uv add`, updated `uv.lock`
- [ ] Did I write code? → Wrote tests (pytest/hypothesis), ran `mutmut run` (score ≥80%)
