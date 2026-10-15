---
name: development-environment-setup
title: Development Environment Setup
description: Set up reproducible, declarative dev environments for AI agent use — mise toolchain, LSPs, PATH integration, per-project pinning
metadata:
  hermes:
    tags: [setup, dev-environment, toolchain, mise]
    related_skills: [preferences-go-development, preferences-rust-development, preferences-typescript-development, preferences-python-development, preferences-kubernetes-administration, preferences-shell-setup]
---

# Development Environment Setup

This guide describes how to set up a development environment optimized for **AI agents** (Hermes, Claude Code, Codex, Cursor, Pi, etc.) to develop code reproducibly. It is not focused on personal interactive editor plugins — editor-specific UI preferences are separate.

## Philosophy

- **Reproducibility first** — the toolchain should be declarative (`mise.toml`), not ad-hoc one-shot installs. Wipe-and-rebuild from config is the goal.
- **Agentic development** — the environment must work for AI agents in non-interactive shell sessions. If a tool isn't on PATH in a plain `bash -l` session, it doesn't exist.
- **Editor-agnostic tools** — LSPs, formatters, linters must be usable by any agent or editor. Never install them only inside an editor's plugin system (e.g., mason.nvim) — they're invisible to anything outside that editor.
- **Prefer mise for everything it supports** — only fall back to language-native toolchains (`go install`, `bun install -g`, `uv tool`) or your system package manager when mise can't handle it.

## Prerequisites

- [mise](https://mise.jdx.dev) — the universal tool version manager
- Your system package manager (apt, pacman, brew, etc.) for Unixy system tools
- The language-specific toolchains you need (installed via mise)

## Toolchain Management

### mise — what goes in global config

On a personal machine (`ben/dev`) or fleet machine (`homelab/mise`), `~/.config/mise` is the adopted git repository after `mise bootstrap --adopt`. The tool list in `~/.config/mise/config.toml` declares the global toolchain (go, rust, node, bun, uv, just, worktrunk, fzf, bat, fd, yq, helm, kubectl, kubie, kubeseal, etc.). Project repos can still pin exact versions in their own `mise.toml`. Run `mise install` after editing the adopted config.

### System package manager — Unixy tools

Use your system package manager for things that don't benefit from version-switching:

```bash
# apt (Debian/Ubuntu)
sudo apt install tmux httpie postgresql-client age

# brew (macOS)
# brew install tmux httpie postgresql age
```

**User preferences note**: Skip fancy git TUIs (lazygit etc.) — prefer CLI git. Don't install Unixy tools like tmux via mise.

### Language-specific tooling

| Stack | Package manager | Notes |
|---|---|---|
| Go | `mise go` | |
| Node/TS | `mise node` + `bun` | Bun is the preferred TS runtime and package manager |
| Rust | `mise rust` | Installs rustup + toolchain; no separate rustup needed |
| Python | `mise uv` | Uv replaces pip/pipx for everything |

## LSP Strategy

Each language's native toolchain manages its own LSP so they're available to any agent or editor:

| LSP | Install command | Managed by | Binary location |
|---|---|---|---|
| gopls | `go install golang.org/x/tools/gopls@latest` | Go toolchain | `~/go/bin/gopls` |
| rust-analyzer | `rustup component add rust-analyzer` | Rust toolchain | `~/.cargo/bin/rust-analyzer` |
| typescript-language-server | `bun install -g typescript-language-server` | Bun | `~/.bun/bin/typescript-language-server` |
| pyright | `uv tool install pyright` | Uv | `~/.local/bin/pyright` |
| TypeScript compiler | `bun install -g typescript` | Bun | `~/.bun/bin/tsc` |

### Bun global bins PATH issue

Bun installs global binaries to `~/.bun/bin/` which is **not** on PATH by default. Fix with:

```bash
for f in ~/.bun/bin/*; do ln -sf "$f" ~/.local/bin/; done
```

`~/.local/bin` should already be on your PATH (it is on most modern systems via `.profile` or `.bashrc`).

## PATH Integration for Agents

This is the key difference between interactive and agentic setups. Agents typically run in non-interactive shell sessions that don't source shell config files. To fix this:

1. **Shell activation comes from bootstrap** — `mise activate` in `~/.zshrc` and `~/.bashrc`, shims in `~/.zshenv` and `~/.bash_profile`. Non-interactive shells use the shims (`~/.local/share/mise/shims`).
2. **Configure your agent to source the shell config** — for Hermes, set `auto_source_bashrc: true` in `~/.hermes/config.yaml`.
3. **Verify**:
   ```bash
   bash -l -c 'which go just node bun rustc'
   ```

**Common pitfall**: `which` can behave differently in an agent's terminal session than in a login shell. Always verify tool availability with `bash -l -c` for accurate results.

## Per-Project Reproducibility

Each project can pin exact versions in a `mise.toml` in its root:

```toml
[tools]
go = "1.25.0"   # exact version, not "latest"
node = "22"
bun = "1.3"
just = "1.50"
```

Combine with a `justfile` — it's the single source of truth for project commands. Agents can read `just --list` or run `just setup` to get started.

## Git Configuration

Bootstrap writes a managed `~/.gitconfig` identity block: `Ben Galusha` / `ben@galu.sh`. Without an identity, commits fail with `Author identity unknown`.

## Related Skills

- `preferences-go-development`
- `preferences-rust-development`
- `preferences-typescript-development`
- `preferences-python-development`
- `preferences-kubernetes-administration`
- `preferences-shell-setup`
- `devlog` & `changelog`
