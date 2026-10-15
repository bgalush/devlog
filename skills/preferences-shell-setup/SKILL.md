---
name: preferences-shell-setup
description: "Use when setting up shell config or managing dotfiles — Ben's preferences for ZSH, alias management, and bootstrapping a personal machine via ben/dev."
version: 1.0.0
author: Ben
license: MIT
metadata:
  hermes:
    tags: [shell, zsh, dotfiles, preferences, ben]
    related_skills: [development-environment-setup]
---

# Shell Setup Preferences

## Overview

ZSH is the primary interactive shell on personal machines. Interactive shell configuration and dotfiles live in the `ben/dev` repository under `dotfiles/`. After `mise bootstrap --adopt`, `ben/dev` is checked out at `~/.config/mise`, and bootstrap symlinks `dotfiles/zsh/config.zsh` to `~/.config/zsh/config.zsh`.

If a setting doesn't survive adopt, it doesn't exist. `ben/dotfiles` is historical and is not cloned.

## Layout in `ben/dev` (`~/.config/mise`)

```
dotfiles/
├── zsh/config.zsh         # sourced from ~/.zshrc
├── starship/
├── ghostty/
├── zed/                   # linked per OS (config.linux.toml / config.macos.toml)
├── environment.d/
├── worktrunk/
└── pacman.txt             # Arch package inventory, not applied
```

### Installation on a Personal Machine

The OS and the `mise` binary come from Ansible (`homelab/ansible`) or OS bootstrap. Then:

```bash
mise bootstrap --adopt https://gitea.core.galu.one/ben/dev.git --yes
```

That installs the declared tools, apt zsh and neovim, writes mise activation (`~/.zshrc` activate, `~/.zshenv` shims), sources `config.zsh`, writes the atuin init block, and sets the login shell to `/usr/bin/zsh`. Do not `chsh` or hand-write `.zshrc`. Do not also adopt `homelab/mise` on a personal machine (fleet servers adopt `homelab/mise`; personal workstations adopt `ben/dev`).

`config.zsh` handles PATH, aliases, completions, prompt, and editor config. Atuin init is the separate `~/.zshrc/atuin` block (`--disable-ai`), not `config.zsh`.

## Alias Management

**All common aliases live in `dotfiles/zsh/config.zsh`** in `ben/dev` (`~/.config/mise`) — never define aliases in `.zshrc` itself or in ad-hoc shell config files on individual machines. `k` / `kc` / `kn` are also mise `[shell_alias]` entries so bash gets them too.

Current categories of aliases in `config.zsh`:

- **File operations**: `ls`, `ll`, `cdp` (cd to Projects)
- **Git**: `cs` (clear + status), `gp`, `ga`, `gc`, `gac`, `gpsup`, `ds`
- **Kubernetes**: `k` (kubectl), `kc` (kubie ctx), `kn` (kubie ns), `kaf`, `krf`
- **Editors**: `vim` → `nvim`, `he` → `helix`, `zed` → `zeditor`, `z` → zed .
- **Tools**: `o` (zed open current dir), `based` (base64 decode), `mc` (mcli), `v` (vault). `step` is the mise binary, not an alias to `step-cli`.

If you add a new alias that's useful across personal machines, add it to `dotfiles/zsh/config.zsh` in `ben/dev` and commit it.

## PATH Management

`config.zsh` manages the following PATH entries in order:

1. `~/.local/bin` — mise tool binaries, uv tool binaries, manual symlinks
2. `~/go/bin` — Go-installed tools (gopls, etc.)
3. `~/.bun/bin` — Bun global binaries
4. `~/.krew/bin` — Kubectl plugins (krew)

Mise tool paths are added by `mise activate` in `.zshrc` and by shims in `.zshenv`, not by `config.zsh`.

## Key Files Outside `dotfiles/`

These are generated or managed by their respective tools:

- `~/.zshrc` — thin loader owned by mise bootstrap (activate block, atuin block, `config.zsh` source)
- `~/.config/mise/` — `ben/dev` checkout after adopt, including `config.toml`
- `~/.config/tea/config.yml` — Gitea CLI config
- `~/.kube/configs/*.yaml` — per-cluster kubeconfigs

## Common Pitfalls

1. **Defining aliases in `.zshrc`** — they'll work, but they won't be tracked or portable. Put them in `dotfiles/zsh/config.zsh`.
2. **Editing `config.zsh` but not committing** — the change is local-only. Commit and push so the next machine gets it.
3. **Hand-writing `.zshrc` instead of letting mise bootstrap manage it** — the activate, atuin, and `config.zsh` source lines come from `config.toml`.
4. **Cloning `ben/dotfiles` or adopting `homelab/mise` on a personal machine** — shell config and the global toolchain both come from `ben/dev`.
5. **Initializing atuin from `config.zsh`** — the managed `~/.zshrc/atuin` block already runs `atuin init zsh --disable-ai`.
6. **Shell config depends on a non-portable path** — `config.zsh` uses `$HOME` for all path references. Hardcoded absolute paths (like `/home/ben/...`) break on a new machine.

## Verification Checklist

- [ ] `~/.config/mise` is a checkout of `gitea.core.galu.one/ben/dev.git`
- [ ] `~/.zshrc` sources `~/.config/zsh/config.zsh`
- [ ] `~/.config/zsh/config.zsh` is a symlink into `~/.config/mise/dotfiles/zsh/config.zsh`
- [ ] `echo $SHELL` returns `/usr/bin/zsh` or similar
- [ ] `which starship` works (prompt)
- [ ] `which atuin` works (shell history)
- [ ] Git aliases work (`cs`, `gp`, `ga`, `gc`)
- [ ] Kubernetes aliases work (`k`, `kc`, `kn`)
- [ ] Editor aliases work (`vim` → `nvim`, `zed` works if installed)
- [ ] `cdp` changes to `~/Projects/`
- [ ] `ls --color=auto` produces colored output (alias active)
