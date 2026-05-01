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

## Usage

Add this function to your shell profile (`~/.bashrc`, `~/.zshrc`) to download templates into a new project:

```bash
get-templates() {
    mkdir -p docs/templates core/tests

    echo "Downloading templates via SSH..."

    git archive --remote=git@github.com:kleber-yokota/template-agents.git add-docs | tar -x docs/template

    if [ -f "docs/template/ROOT_OVERVIEW.md" ]; then
        cp docs/template/ROOT_OVERVIEW.md OVERVIEW.md
        echo "OVERVIEW.md created in root."
    fi

    echo "Templates ready."
}
```

Usage:

```bash
# In a new project directory
get-templates
```

To use HTTPS instead of SSH, replace the remote URL with:

```
https://github.com/kleber-yokota/template-agents.git
```

## Documentation

| File | Purpose |
|---|---|
| `AGENTS.md` | Architectural standards and documentation rules |
| `TESTING_GUIDE.md` | Testing protocols (pytest, hypothesis, mutmut, atheris) |
| `REVIEW.md` | Code review guidelines and checklists |
| `docs/templates/` | Starter templates for new modules |

## License

This project is licensed under the [Apache License 2.0](LICENSE).
