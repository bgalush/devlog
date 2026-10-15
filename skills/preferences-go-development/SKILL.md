---
name: preferences-go-development
description: "Use when writing, testing, or building Go — Ben's preferences for mise-managed toolchain, gopls LSP, project structure, dependency workflow."
version: 1.0.0
author: Ben
license: MIT
metadata:
  hermes:
    tags: [go, golang, development, preferences, ben]
    related_skills: [development-environment-setup, preferences-rust-development, preferences-typescript-development, preferences-python-development]
---

# Go Development Preferences

## Overview

Go is a primary backend language. The toolchain is managed by **mise** — pin the version you need in `~/.config/mise/config.toml` or a project-level `mise.toml`. All Go tools are available via mise shims (or directly if their install paths are on `$PATH`).

## When to Use

- Building CLI tools, API servers, or backend services
- Any task where a compiled, typed language with fast compile times is the right fit

## Toolchain

- **Go**: `mise go` — version as declared in mise config
- **gopls**: LSP installed via `go install golang.org/x/tools/gopls@latest` — managed by Go itself
- **Build**: `go build ./...`
- **Test**: `go test ./...`
- **Format**: `go fmt ./...`
- **Vet**: `go vet ./...`

## Project Setup

Initialise a project with a `mise.toml` if you need a specific Go version:

```toml
[tools]
go = "1.25.0"
```

Run `mise install` to fetch that version.

## Project Structure

```
myproject/
├── cmd/             # Entry points (cmd/server/main.go)
├── internal/        # Private packages (not importable outside)
├── pkg/             # Public library packages
├── go.mod
└── mise.toml        # Optional: pin Go version
```

## Conventions

- Avoid `init()` functions unless absolutely necessary
- Prefer `errors.New()` over `fmt.Errorf()` for simple sentinel errors
- Use `fmt.Errorf("...: %w", err)` for wrapped errors
- Use `context.Context` as first parameter in public functions that do I/O
- Return concrete types, accept interfaces
- Table-driven tests preferred

## Common Pitfalls

1. **Running `go install` without `@latest`** — always pin with `@latest` or a specific version like `@v0.21.0`
2. **Forgetting `go mod tidy`** — CI and builds will fail with dirty go.sum
3. **Using `go get -u` blindly** — can upgrade transitive deps unexpectedly; prefer targeted `go get <pkg>@<version>`
4. **Embedding with `//go:embed`** — the embedded file paths must be relative to the source file's directory, and the UI build must run before `go build`

## Verification Checklist

- [ ] `go version` returns the expected version
- [ ] `go env GOROOT` points to mise install path (if using mise)
- [ ] `gopls version` works (LSP)
- [ ] `go build ./...` succeeds with no errors
- [ ] `go vet ./...` passes
- [ ] `go mod tidy` produces clean go.sum
