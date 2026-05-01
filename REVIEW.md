# REVIEW.md — Code Review Guide

## Purpose

Standard guide for code review across all project modules. Reviews ensure quality, clarity, and clean architecture.

## Scope

All rules below apply **only to project code** (`tlc_downloader/`, `math_new/`, `new_string/`, etc.). They do **not** apply to:

- Third-party libraries in `.venv/` or `site-packages/`
- Generated files or auto-generated code
- Infrastructure files (`docker-compose.yml`, etc.)

---

## Code Structure

### One Class Per File

Each file must contain **at most one class**.

Exceptions are documented per-module in the module's `REVIEW_EXCEPTION.md`.

### Single Responsibility

A class must not do too many things. If a class fetches, cleans, validates, AND saves data, it needs to be split.

**Composition is OK.** A class that delegates to other classes has well-defined responsibilities and is acceptable.

**Accumulation is NOT OK.** A class that does everything without delegation needs to be split.

### Methods

| Signal | Action |
|--------|--------|
| >5 public methods | Evaluate whether the class can be split |
| Many private methods | Likely too many responsibilities — analyze for extraction |

Private methods reveal hidden complexity. A class that hides many private methods is doing work it should delegate to separate classes.

### File Size

| File type | Max lines | Action |
|-----------|-----------|--------|
| Test files | 200 | Split into multiple files |
| Source files | 150 | Split by extracting related code into separate files |

If a file approaches this limit, extract related code into separate files or classes for composition.

---

## Imports

### Top-Level Only

All imports must be at the top of the file, at the module level. Imports inside functions or methods are prohibited — they signal that a module dependency should be declared at the top instead.

### No Duplicates

Avoid loading the same library or module multiple times. If a module is imported in several functions within the same file, move it to the top.

### No `sys.path` Manipulation

Check for `sys.path.insert` or `sys.path.append` in any file. These are always wrong — they signal that the module layout was designed incorrectly.

**Fix:** Move the script outside the module (to the project root) or use a proper entry point (`__main__.py`).

The only acceptable exceptions (imports inside functions, `sys.path` manipulation) must be documented in the module's `REVIEW_EXCEPTION.md`.

### Import Hierarchy

Files in subdirectories (submodules) must NOT import from parent directories.

**Allowed imports from `module/submodule/file.py`:**
- `from . import something` — same level
- `from .subsub import something` — sub-subdirectory (below)
- `from . import sibling_module` — same level

**Prohibited imports from `module/submodule/file.py`:**
- `from .. import something` — parent directory
- `from ..sibling_module import something` — sibling of parent

```
module/
├── __init__.py          ← top-level: can import from anywhere in this module
├── top_level.py         ← top-level: can import from anywhere in this module
├── submodule_a/
│   ├── __init__.py      ← submodule: can import from . (same level only)
│   └── file.py          ← submodule: can import from . (same level only)
└── submodule_b/
    ├── __init__.py      ← submodule: can import from . (same level only)
    └── file.py          ← submodule: can import from . (same level only)
```

If a submodule needs shared code, inline the shared definitions directly in each submodule file. Small interfaces (1–2 methods) and simple dataclasses are acceptable to duplicate.

### Submodule Shared Code

When submodules within the same module share interfaces or classes (e.g., `ragas/adapter.py` and `deepeval/adapter.py` both need an `Adapter` base class), the shared code must be inlined in each submodule file — since `from ..shared` imports from the parent directory, which is prohibited.

If submodules need significant shared code (not just small interfaces), consider flattening the module structure so files are at the same level, OR inline the shared definitions directly.

### Nested Modules

Nested modules are allowed with no depth limit. This is the primary mechanism for organizing shared code and related components when a module grows beyond a flat structure.

When a module has shared code used by multiple components, create a submodule for that shared code.

```
dataset_generator/
├── __init__.py              ← top-level: can import from anywhere
├── shared/                  ← submodule for shared code
│   ├── __init__.py          ← re-exporter
│   ├── models.py            ← data models
│   ├── interfaces.py        ← shared interfaces
│   ├── config.py            ← configuration
│   └── generator.py         ← LLM client and generation logic
├── adapters.py              ← imports from .shared
├── exporters.py             ← imports from .shared
├── generate_ragas.py        ← imports from .shared + .adapters
└── generate_deepeval.py     ← imports from .shared + .adapters
```

When splitting a file that contains multiple classes:
1. If the new files all relate to the same logical concern (e.g., adapters for different formats), create a single file with all of them
2. If the number of format-specific implementations grows significantly (>4), create a submodule (e.g., `adapters/` or `formatters/`)

---

## Code Quality

### Function Length

Functions (including methods) must not exceed **15 lines of actual code** (excluding docstrings and blank lines).

**When counting lines, exclude:**
- Docstrings (triple-quoted strings)
- Blank lines
- Lines containing only comments (recommended)

This rule applies to both classes and standalone functions. Many small focused functions are preferred over fewer long ones.

### Expected Output Documentation

Every function and method **must** document its expected output in the docstring.

**Required docstring structure:**

```python
def function_name(param: str) -> ReturnType:
    """One-line description.

    Args:
        param: Description of the parameter.

    Returns:
        Description of the return value including its type and meaning.

    Raises:
        SomeError: Condition that triggers this exception.
    """
```

**Review checklist:**
- [ ] Every public function/method has a docstring
- [ ] Docstring includes a `Returns:` section describing the return value
- [ ] Return type annotation matches the docstring description
- [ ] `Raises:` section documents exceptions with trigger conditions
- [ ] Functions returning lists document filtering/clearing behavior
- [ ] Functions returning None document side effects or early return conditions

---

## Review Files

| File | Location | Purpose |
|------|----------|---------|
| `REVIEW.md` | Root | This file — review guide |
| `docs/template/REVIEW_EXCEPTION.md` | Template | Copy to each module for exceptions |

Each module should copy `docs/template/REVIEW_EXCEPTION.md` to `<module>/REVIEW_EXCEPTION.md` if it needs exceptions. If a module has no exceptions, it does not need this file.

### When to create `REVIEW_EXCEPTION.md`

- A legacy file violates the one-class-per-file rule
- A function exceeds 15 lines (e.g., third-party API wrapper)
- Lazy imports are required (circular dependency workaround)
- Any other rule that cannot be followed in practice
