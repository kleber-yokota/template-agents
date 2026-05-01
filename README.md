# Template Agents

Architectural templates and standards for AI Agentic Workflows (OpenCode/Zed), built with Python and uv.

## Overview

This project establishes modular documentation rules (OVERVIEW/DETAILS) and rigorous quality protocols, including Mutation Testing (Mutmut), Property-based testing, and Fuzzing (Atheris). Optimized for self-hosted environments.

## Features

- **Modular structure** — Each module requires `OVERVIEW.md` and `DETAILS.md`
- **Quality assurance** — Mutation testing, property-based tests, and fuzzing
- **Python tooling** — Uses `uv` for dependency and environment management
- **Self-hosted ready** — Designed for self-hosted AI agent environments

## Quick Start

1. Clone the repository
2. Copy templates from `docs/templates/` into new modules
3. Follow the documentation and testing standards defined in `AGENTS.md` and `TESTING_GUIDE.md`

## Documentation

| File | Purpose |
|---|---|
| `AGENTS.md` | Architectural standards and documentation rules |
| `TESTING_GUIDE.md` | Testing protocols (pytest, hypothesis, mutmut, atheris) |
| `docs/templates/` | Starter templates for new modules |

## License

[Add license information]
