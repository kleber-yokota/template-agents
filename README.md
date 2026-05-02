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
get-agents() {
  local user="kleber-yokota"
  local repo="template-agents"
  local branch="main"
  
  # Fetch file list from GitHub API with User-Agent to avoid 403/404 errors
  local entries=$(curl -s -H "User-Agent: curl" "https://api.github.com/repos/$user/$repo/git/trees/$branch?recursive=1" | \
                  grep '"path":' | \
                  sed -E 's/.*"path": "([^"]+)".*/\1/')

  if [ -z "$entries" ]; then
    echo "Error: Could not retrieve file list from repository."
    echo "Please check if the branch '$branch' exists or if the API rate limit was reached."
    return 1
  fi

  echo "Synchronizing files (skipping README and LICENSE)..."

  while read -r file; do
    if [ -n "$file" ]; then
      # Convert to lowercase for comparison
      local low_file="${file,,}"
      
      # Skip README and LICENSE (any extension)
      if [[ "$low_file" == "readme.md" || "$low_file" == "license" || "$low_file" == "license.md" || "$low_file" == "license.txt" ]]; then
        continue
      fi

      # Download and create directories if they don't exist
      curl -sfL "https://raw.githubusercontent.com/$user/$repo/$branch/$file" \
           --create-dirs \
           -o "$file"
      
      if [ $? -eq 0 ]; then
        echo "Downloaded: $file"
      fi
    fi
  done <<< "$entries"

  echo "Synchronization complete."
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
