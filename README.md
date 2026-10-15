# devstack

Agent skills for engineering records (`devlog`, `changelog`) and reproducible development workflows across languages and toolchains.

## Skills

### Engineering Workflow & Records
- **[`devlog`](skills/devlog/SKILL.md)** — Maintain repository-local engineering records in `devlog/`: Architecture Decision Records (`ADR`), multi-phase implementation plans (`PLAN`), and research spikes or investigations (`DIG`).
- **[`changelog`](skills/changelog/SKILL.md)** — Maintain a top-level `CHANGELOG.md` index and detailed release notes in `changelogs/vX.Y.Z.md`, linking shipped changes back to the `devlog/` records that drove them.

### Development Environment & Language Preferences
- **[`development-environment-setup`](skills/development-environment-setup/SKILL.md)** — Set up reproducible, declarative dev environments for AI agents (`mise`, editor-agnostic LSPs, non-interactive `PATH` integration, per-project `mise.toml` + `justfile`).
- **[`preferences-go-development`](skills/preferences-go-development/SKILL.md)** — Go toolchain via `mise`, `gopls`, project structure, error handling, and verification checklist.
- **[`preferences-rust-development`](skills/preferences-rust-development/SKILL.md)** — Rust toolchain via `mise`, `cargo` / `clippy` / `rust-analyzer`, `anyhow`/`thiserror` conventions.
- **[`preferences-typescript-development`](skills/preferences-typescript-development/SKILL.md)** — TypeScript/JavaScript with `bun`, `tsc --noEmit`, `typescript-language-server`, and ESM conventions.
- **[`preferences-python-development`](skills/preferences-python-development/SKILL.md)** — Python workflows with `uv` (never raw `pip`), `pyright`, `ruff`, and inline `# /// script` dependencies.
- **[`preferences-kubernetes-administration`](skills/preferences-kubernetes-administration/SKILL.md)** — `mise`-managed `kubectl`, `helm`, `kubie` (`~/.kube/configs/*.yaml`), and `kubeseal`.
- **[`preferences-shell-setup`](skills/preferences-shell-setup/SKILL.md)** — ZSH configuration, alias management, and personal workstation bootstrap conventions.

## Install

Install with the [Vercel skills CLI](https://github.com/vercel-labs/skills):

```bash
bunx skills add bgalush/devstack
```
