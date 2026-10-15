---
name: preferences-rust-development
description: "Use when writing, testing, or building Rust — Ben's preferences for mise-managed toolchain via rustup, cargo workflow, rust-analyzer LSP, project structure."
version: 1.0.0
author: Ben
license: MIT
metadata:
  hermes:
    tags: [rust, cargo, development, preferences, ben]
    related_skills: [development-environment-setup, preferences-go-development, preferences-typescript-development, preferences-python-development]
---

# Rust Development Preferences

## Overview

Rust is used for systems-level programming and performance-critical tools. The toolchain is managed by **mise** via its `rust` core plugin, which wraps rustup internally. All standard Rust tools (`rustc`, `cargo`, `rustup`) are available through mise shims.

## When to Use

- Systems programming, CLI tools where performance matters
- Replacing C/C++ tooling
- Projects where memory safety and zero-cost abstractions are valuable
- Experimental or performance-sensitive infrastructure components

## Toolchain

- **Rust**: `mise rust` — version as declared in mise config
- **rust-analyzer**: LSP installed via `rustup component add rust-analyzer` — binary at `~/.cargo/bin/rust-analyzer` (added to PATH by mise)
- **Build**: `cargo build`
- **Release**: `cargo build --release`
- **Test**: `cargo test`
- **Format**: `cargo fmt`
- **Lint**: `cargo clippy`
- **Docs**: `cargo doc --open`

## Project Setup

Initialise a project with a `mise.toml` if you need a specific Rust version:

```toml
[tools]
rust = "1.80.0"
```

Run `mise install` to fetch that version.

## Project Structure

```
myproject/
├── src/
│   ├── main.rs        # Binary entry point
│   └── lib.rs         # Library root (for library crates)
├── tests/             # Integration tests
├── benches/           # Benchmarks
├── Cargo.toml
└── mise.toml          # Optional: pin Rust version
```

For multi-crate projects, use a `cargo workspace`.

## Conventions

- Use `anyhow::Result` for error handling in binaries
- Use `thiserror` for library error types (custom enums)
- Use `cargo clippy` as the primary lint — address warnings before committing
- Prefer `cargo fmt` before every commit
- Use `cargo deny` or `cargo audit` for security-sensitive projects
- Module structure: one module per file, avoid `mod.rs` — use `module_name/mod.rs` or `module_name.rs`

## Common Pitfalls

1. **Rust installed directly via rustup without mise** — `mise install rust` wraps rustup but if you ran rustup independently, versions may conflict. Check with `mise ls rust`.
2. **Forgetting `cargo fmt`** — CI will reject unformatted code
3. **Over-optimizing early** — prefer idiomatic, readable Rust over clever unsafe code
4. **Ignoring clippy warnings** — clippy exists for a reason; `#[allow(...)]` should have a comment explaining why
5. **Missing `~/.cargo/bin` on PATH** — mise adds it, but if you're testing outside a sourced shell, tools like `rust-analyzer` may not resolve

## Verification Checklist

- [ ] `rustc --version` matches the mise-managed version
- [ ] `cargo --version` works
- [ ] `rust-analyzer --version` works (LSP)
- [ ] `cargo build` succeeds with no errors
- [ ] `cargo test` passes
- [ ] `cargo clippy` produces no warnings (or documented exceptions)
- [ ] `cargo fmt --check` passes
