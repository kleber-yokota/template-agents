# [project-name] — System Map

> **For the agent**: This is the documentation entry point.
> Read this file to understand the overall architecture before navigating to any module.
> Each module has its own `OVERVIEW.md` with details about its internal flow.

---

## What this system does

<!-- One or two sentences. What problem it solves and in what context. -->
`[project]` is responsible for **[main responsibility]**.
It receives **[input]** and produces **[output]**, orchestrating the modules below.

---

## Modules

| Module | Responsibility | Doc |
|---|---|---|
| `auth/` | Authentication and token generation | [OVERVIEW](auth/OVERVIEW.md) |
| `pipeline/` | Orchestrates the main processing flow | [OVERVIEW](pipeline/OVERVIEW.md) |
| `repository/` | Database access | [OVERVIEW](repository/OVERVIEW.md) |
| `notifier/` | Sends notifications (email, webhook) | [OVERVIEW](notifier/OVERVIEW.md) |
| `scheduler/` | Job scheduling and execution | [OVERVIEW](scheduler/OVERVIEW.md) |

---

## High-level flow

```
                        ┌─────────────┐
            request ───►│    auth/    │── invalid token ──► 401
                        └──────┬──────┘
                               │ valid token
                               ▼
                        ┌─────────────┐
                        │  pipeline/  │◄── external data (API)
                        └──────┬──────┘
                    ┌──────────┼──────────┐
                    ▼          ▼          ▼
             ┌──────────┐  ┌──────┐  ┌──────────┐
             │repository│  │ ...  │  │ notifier │
             └──────────┘  └──────┘  └──────────┘
```

---

## Module dependencies

> Read as: `A → B` means "A depends on B" (A imports from B).

```
pipeline/   → repository/
pipeline/   → notifier/
scheduler/  → pipeline/
auth/       → repository/
```

**Dependency rules:**
- `repository/` does not import any other internal module
- `notifier/` does not import any other internal module
- Circular dependencies are forbidden

---

## Cross-module contracts

> These are the types that cross module boundaries.
> Changes here affect multiple modules — edit with care.

### `auth/` → `pipeline/`

```python
# Defined in: auth/models.py
# Consumed in: pipeline/core.py

@dataclass
class TokenPayload:
    user_id: str
    scopes: list[str]          # e.g. ["read", "write"]
    expires_at: datetime
```

---

### `pipeline/` → `repository/`

```python
# Defined in: pipeline/models.py
# Consumed in: repository/queries.py

@dataclass
class RecordInput:
    user_id: str
    payload: dict
    source: str                # e.g. "api", "scheduler"
    created_at: datetime

@dataclass
class SavedRecord:
    id: str
    user_id: str
    payload: dict
    created_at: datetime
```

---

### `pipeline/` → `notifier/`

```python
# Defined in: pipeline/models.py
# Consumed in: notifier/sender.py

@dataclass
class NotificationEvent:
    type: str                  # e.g. "completed", "error"
    user_id: str
    data: dict
    timestamp: datetime
```

---

### `scheduler/` → `pipeline/`

```python
# Defined in: scheduler/models.py
# Consumed in: pipeline/core.py

@dataclass
class JobConfig:
    job_id: str
    parameters: dict
    scheduled_for: datetime
    retry_max: int = 3
```

---

## Global configuration

All modules read configuration from `settings.py` at the root:

```python
# settings.py
DATABASE_URL: str
EXTERNAL_API_KEY: str
NOTIFIER_WEBHOOK_URL: str
LOG_LEVEL: str = "INFO"
```

No module defines its own configuration — everything comes from `settings.py`.

---

## Where things live

```
project/
├── OVERVIEW.md              ← you are here
├── settings.py              # global config
├── main.py                  # entrypoint
│
├── auth/
│   ├── OVERVIEW.md
│   ├── DETAILS.md
│   └── ...
│
├── pipeline/
│   ├── OVERVIEW.md
│   ├── DETAILS.md
│   └── ...
│
├── repository/
│   ├── OVERVIEW.md
│   ├── DETAILS.md
│   └── ...
│
├── notifier/
│   ├── OVERVIEW.md
│   ├── DETAILS.md
│   └── ...
│
└── tests/
    └── integration/         # tests that cross module boundaries
```

---

## What is NOT documented here

- Function and class details → `[module]/DETAILS.md`
- Internal module behavior → `[module]/OVERVIEW.md`
- Per-module design decisions → `[module]/DETAILS.md#design-decisions`

---

## Quick reference for the agent

| I want to understand... | Read |
|---|---|
| The system as a whole | This file |
| The flow of a specific module | `[module]/OVERVIEW.md` |
| A function signature | `[module]/DETAILS.md` |
| A type that crosses modules | "Contracts" section above |
| How to run the tests | `[module]/DETAILS.md#how-to-run-the-tests` |
| Available configuration | `settings.py` |
