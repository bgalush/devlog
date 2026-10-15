---
name: preferences-python-development
description: "Use when writing, testing, or running Python — Ben's preferences for uv for everything, pyright LSP, inline script patterns, project setup."
version: 1.0.0
author: Ben
license: MIT
metadata:
  hermes:
    tags: [python, uv, development, preferences, ben]
    related_skills: [development-environment-setup, preferences-go-development, preferences-rust-development, preferences-typescript-development]
---

# Python Development Preferences

## Overview

**uv** is the single entry point for everything Python: project management, virtual environments, tool installation, and script execution. **Do not use `pip` directly** — `uv` is faster, produces deterministic lockfiles, and handles tool isolation automatically. The system Python version is whatever comes with the OS (check with `python3 --version`).

## When to Use

- Scripting, automation, data processing
- Prototyping, glue code between services
- ML/AI tooling
- Any task where Python's ecosystem is the bottleneck to move fast

## Toolchain

- **Python**: system default (don't install a separate Python via mise unless needed)
- **Package manager**: `uv` — managed by mise
- **LSP**: `pyright` — installed via `uv tool install pyright`, binary goes to `~/.local/bin/pyright`
- **Format/Lint**: `ruff` — install per-project with `uv add --dev ruff`

## Project Setup

Initialise a project with a specific Python version if needed:

```bash
uv python install 3.12
```

## Tool Management

Use `uv tool` for globally-installed Python tools (LSPs, formatters, linters). Tools are installed in isolated environments:

```bash
uv tool install pyright       # Python LSP
uv tool install ruff          # Linter + formatter
uv tool list                  # list installed tools
```

Tool binaries go to `~/.local/bin/`.

## Conventions

- **Type hints** on all function signatures and public APIs
- Use `pathlib.Path` over `os.path` for all file operations
- Use `subprocess.run(cmd, check=True, capture_output=True, text=True)` over bare subprocess calls
- Scripts with dependencies use the inline `# /// script` header format
- Configuration in `pyproject.toml` (not `setup.py`, `setup.cfg`, or `.ini` files)
- Prefer `ruff` over `black` and `flake8` — it's 10-100x faster

## Common Pitfalls

1. **Using `pip` instead of `uv`** — don't. `uv pip` is an escape hatch for complex scenarios, but `uv add` / `uv sync` is the primary workflow.
2. **Omitting `uv.lock` from version control** — without it, builds are non-deterministic
3. **Creating a venv manually with `python3 -m venv`** — use `uv venv` instead (faster and handles Python version selection)
4. **Installing tools with `pip install --user`** — use `uv tool install` instead; it isolates tools from each other
5. **Forgetting `# /// script` header syntax** — the shebang `#!` must come before the `# /// script` block, and the block must be at the top of the file
6. **System Python may be older than your project needs** — use `uv python install 3.12 && uv venv --python 3.12` to install and use a different version

## Verification Checklist

- [ ] `uv --version` works
- [ ] `pyright --version` works (LSP)
- [ ] `uv tool list` shows installed tools
- [ ] `uv sync` produces a clean `uv.lock`
- [ ] No `pip` or `pip3` usage in workflows
