---
name: preferences-typescript-development
description: "Use when writing, testing, or building TypeScript/JavaScript — Ben's preferences for Bun as runtime and package manager, tsc for type-checking, typescript-language-server LSP."
version: 1.0.0
author: Ben
license: MIT
metadata:
  hermes:
    tags: [typescript, javascript, bun, node, development, preferences, ben]
    related_skills: [development-environment-setup, preferences-go-development, preferences-rust-development, preferences-python-development]
---

# TypeScript/JavaScript Development Preferences

## Overview

**Bun** is the primary runtime and package manager for JavaScript/TypeScript. Node.js is available as a fallback if needed. The TypeScript compiler (`tsc`) and `typescript-language-server` are installed globally for strict type-checking and LSP support.

## When to Use

- Web frontends (SvelteKit, Hono, plain HTML/CSS/JS)
- API servers, CLI tools written in TypeScript
- Build scripts, automation, project tooling

## Toolchain

- **Runtime/PM**: `bun` — managed by mise
- **TypeScript compiler**: `tsc` — installed via `bun install -g typescript`
- **LSP**: `typescript-language-server` — installed via `bun install -g typescript-language-server`
- **Node.js**: available as fallback via mise

Bun installs global binaries to `~/.bun/bin/` which may not be on PATH by default. Symlink them to a directory on PATH (like `~/.local/bin/`):

```bash
for f in ~/.bun/bin/*; do ln -sf "$f" ~/.local/bin/; done
```

## Project Setup

Initialise via mise — Bun is already available. If you need a specific version:

```toml
[tools]
bun = "1.3"
node = "22"
```

Run `mise install` to fetch pinned versions.

## TypeScript Configuration

Projects should use strict mode in `tsconfig.json`. For SvelteKit, generate via `bun create svelte` — it produces the correct config automatically.

## Conventions

- Use `import`/`export` (ES modules), never `require`
- Use `async/await`, avoid raw promises or callbacks
- Prefer `const` over `let`; never use `var`
- Named exports over default exports (better tree-shaking and refactoring)
- Use `Bun.file()` and `Bun.write()` for file I/O
- Use `Bun.serve()` for lightweight HTTP servers without a framework
- TypeScript strict mode is expected; avoid `any` unless there's a documented reason

## Common Pitfalls

1. **`bun install -g` bins not on PATH** — Bun installs globals to `~/.bun/bin/` which may not be on PATH by default. Symlink them to `~/.local/bin/` or add `~/.bun/bin` to your PATH.
2. **Type-checking with Bun vs tsc** — Bun runs TS natively without type-checking. Always run `tsc --noEmit` separately to catch type errors.
3. **Lockfile format** — `bun.lock` is a binary file; don't try to diff it manually. Just commit it.
4. **Mixing Bun and npm/pnpm/yarn** — stick to Bun for a project; don't mix package managers in the same project or the lockfile will conflict.
5. **SvelteKit projects** — use `bun run check` (not `tsc --noEmit`) for full type-checking including Svelte files.

## Verification Checklist

- [ ] `bun --version` matches expected
- [ ] `tsc --version` works
- [ ] `typescript-language-server --version` works
- [ ] `bun install` produces a clean `bun.lock`
- [ ] Global bun binaries are accessible on PATH
