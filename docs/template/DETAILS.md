# [module-name] — Implementation Details

> **For the agent**: This file contains technical details.
> Start with `OVERVIEW.md` if you haven't read it yet.

---

## Public API

### `MyClass`

```python
class MyClass:
    def __init__(self, config: Config) -> None: ...

    def run(self, data: dict) -> ResultModel:
        """
        Description of what it does.

        Args:
            data: Dictionary with fields x, y, z.

        Returns:
            ResultModel with fields a, b.

        Raises:
            ValueError: If data is incomplete.
            RuntimeError: If the external connection fails.
        """
        ...

    def reset(self) -> None:
        """Resets internal state. Safe to call multiple times."""
        ...
```

### `util_function(x, y)`

```python
def util_function(x: int, y: str) -> bool:
    """
    Returns True if x > 0 and y is not empty.
    Has no side effects.
    """
    ...
```

---

## Models / Types

```python
from dataclasses import dataclass, field
# or: from pydantic import BaseModel

@dataclass
class ResultModel:
    success: bool
    data: dict
    errors: list[str] = field(default_factory=list)

@dataclass
class Config:
    timeout: int = 30
    retry: int = 3
    base_url: str = "https://api.example.com"
```

---

## Expected behavior (test cases)

```python
# Happy path
result = obj.run({"x": 1, "y": "ok"})
assert result.success is True

# Invalid input
with pytest.raises(ValueError):
    obj.run({})

# Retry on network failure — should try 3x before raising
with respx.mock:
    respx.get(...).mock(side_effect=httpx.ConnectError)
    with pytest.raises(RuntimeError):
        obj.run(valid_data)
```

---

## Edge cases and gotchas

- **`data` with extra keys**: silently ignored — does not raise
- **`timeout` = 0**: disables timeout (careful in production)
- **Thread safety**: `MyClass` is **not thread-safe**. Create one instance per thread
- **Order matters**: call `reset()` before reusing the instance with different data

---

## Design decisions

| Decision | Alternative considered | Why this one |
|---|---|---|
| Pydantic for validation | plain dataclass | Automatic validation + JSON serialization |
| `httpx` instead of `requests` | `requests` | Async support and HTTP/2 |
| State on the instance | Stateless + pure functions | Easier retry without re-parsing config |

---

### Test structure

```
tests/
├── conftest.py          # Shared fixtures (config, mocks)
├── test_core.py         # Tests MyClass
├── test_utils.py        # Tests util_function and helpers
└── test_integration.py  # Tests full flow (real network or vcr)
```

---

## Logs and observability

```python
import logging
logger = logging.getLogger(__name__)

# Module logs at DEBUG level what it is processing
# At WARNING level when retrying
# At ERROR level when it fails definitively
```

To see logs during tests:
```bash
pytest tests/ -s --log-cli-level=DEBUG
```

---

## Relevant change history

| Date | What changed | Why |
|---|---|---|
| 2024-01 | Added automatic retry | External API instability |
| 2024-03 | Migrated to Pydantic v2 | Performance + better ergonomics |
