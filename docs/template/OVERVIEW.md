# [module-name] — Overview

> **For the agent**: Read this file first. It describes the module's general flow.
> If you need implementation details, refer to `DETAILS.md`.

---

## What this module does

<!-- One sentence. What it solves, for whom, and in what context. -->
`[module]` is responsible for **[main responsibility]** within the `[system name]` system.

---

## Main flow

```
[input] → [step 1] → [step 2] → [step 3] → [output]
```

### Concrete example

```python
# How this module is typically used
from module import MyClass

obj = MyClass(config=...)
result = obj.run(data)
# result: { ... }
```

---

## File structure

```
module/
├── __init__.py          # Exports: MyClass, util_function
├── core.py              # Main logic
├── models.py            # Dataclasses / Pydantic models
├── utils.py             # Internal helpers
└── tests/
    ├── test_core.py
    └── test_utils.py
```

---

## Inputs and outputs

| Input | Type | Output | Type |
|---|---|---|---|
| `data` | `dict` | `result` | `ResultModel` |
| `config` | `Config` | errors | `ValueError` |

---

## External dependencies

- `httpx` — HTTP calls
- `pydantic` — data validation
- *(add as needed)*

---

## Possible states / Lifecycle

```
INITIAL → PROCESSING → COMPLETED
                     ↘ ERROR
```

---

## What is NOT in this module

<!-- Important so the agent doesn't look for the wrong thing here -->
- Authentication → see `auth/`
- Database persistence → see `repository/`
- Global configuration → see `settings.py`

---

## Files to read if you need more context

| File | Why read it |
|---|---|
| `DETAILS.md` | Function signatures, edge cases, design decisions |
| `tests/test_core.py` | Expected behavior with real examples |
| `models.py` | Data structure definitions |
