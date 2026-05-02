# [module-name]/REVIEW_EXCEPTION.md — Review Exceptions

> **For the agent**: This file documents exceptions to the rules defined in the root `REVIEW.md`.
> Copy this template to `<module>/REVIEW_EXCEPTION.md` and fill in the table if needed.
> If a module has no exceptions, this file does not need to exist.

---

## Purpose

Document exceptions to the rules defined in the root `REVIEW.md` for this specific module.

## Rules

- Exceptions are **module-specific** — there is no root `REVIEW_EXCEPTION.md`
- Every exception must include: the rule being waived, the affected file(s), and the reason
- If no exceptions exist, this file does not need to be created

## Exceptions

| Rule | File(s) | Reason |
|------|---------|--------|
| | | |

## Examples

```markdown
| Rule | File(s) | Reason |
|------|---------|--------|
| One Class Per File | `multi_class_helper.py` | Legacy file — 2 small utility classes used together |
| Function Length (15 lines) | `legacy_parser.py` | Third-party API wrapper — cannot split without breaking contract |
| Top-Level Imports Only | `dynamic_loader.py` | Imports must be lazy to avoid circular dependency with `config.py` |
```
