---
name: preferences-kubernetes-administration
description: "Use when setting up or verifying k8s tooling on a fresh machine — Ben's preferences for mise-managed kubectl/helm/kubie/kubeseal, config structure, and installation checks."
version: 1.0.0
author: Ben
license: MIT
metadata:
  hermes:
    tags: [kubernetes, helm, kubectl, kubie, k8s, preferences, ben]
    related_skills: [development-environment-setup, preferences-go-development, preferences-rust-development, preferences-typescript-development, preferences-python-development]
---

# Kubernetes Administration Preferences

## Overview

All Kubernetes tooling is installed and version-managed by mise — `kubectl`, `helm`, `kubie`, and `kubeseal` come from `~/.config/mise/config.toml` and are available via mise shims. No manual downloads, no apt repos, no Homebrew taps.

## When to Use

- Setting up k8s tooling on a new machine
- Verifying the toolchain is working after installation
- Understanding the preferred config file layout

## Toolchain

Tools are declared in mise global config:

```toml
[tools]
helm = "4"              # Helm v4.x (use major version pin)
kubectl = "latest"      # latest stable kubectl
kubie = "latest"        # context/namespace manager
kubeseal = "latest"     # sealed secrets CLI
```

Run `mise install` to fetch them.

- **kubectl**: primary cluster interface
- **helm**: package manager for Kubernetes applications
- **kubie**: replaces raw `kubectl config use-context` — provides per-shell context and namespace isolation
- **kubeseal**: encrypts Secrets into SealedSecrets for git-safe storage

## Config File Layout

```
~/.kube/
├── configs/            # One file per cluster, not one merged file
│   ├── core.yaml       # k3s cluster config
│   ├── sidney.yaml
│   └── production.yaml
└── config              # Optional: kubectl's default config (keep minimal)
```

Each file in `configs/` is a standard kubeconfig YAML. Kubie reads from this directory; it never writes to it.

Kubie's own config lives at `~/.config/kubie/config.yaml` if you need to set defaults (e.g., default namespace per context).

## Verification

After installing via mise, confirm the toolchain is wired up:

```bash
kubectl version --client          # should show client version, no warnings
helm version                      # should show version string
kubie version                     # should show version
kubeseal version                  # should show version
ls ~/.kube/configs/               # should show at least one cluster config
kubie ctx                         # should list available contexts (may be empty)
```

If `kubie ctx` is empty, you don't have any kubeconfigs yet. Drop one in `~/.kube/configs/` from a cluster you have access to.

## Conventions

- **Use kubie for all context/namespace switching** — never modify the default kubeconfig with `kubectl config set-context`. Kubie's per-shell isolation prevents accidentally running commands in the wrong cluster.
- **One kubeconfig per cluster** — separate files in `~/.kube/configs/`, not a single merged file with dozens of contexts.
- **Helm uses major version pinning** — `helm = "4"` in mise config (not `latest`) to avoid unexpected major version bumps.
- **Always check `helm diff` is available** — install the helm-diff plugin for upgrade previews. Verify with `helm plugin list`.
- **Do not edit kubeconfigs with kubectl** — use kubie or edit the YAML directly. `kubectl config` commands often reformat or merge files in unexpected ways.

## Common Pitfalls

1. **KUBECONFIG env var overrides kubie** — if `KUBECONFIG` is set in your shell, kubie ignores `~/.kube/configs/`. Unset it or use `unset KUBECONFIG` in your shell config.
2. **kubectl context leakage without kubie** — running `kubectl config use-context` in one terminal affects all terminals. Kubie fixes this by spawning a subshell per context.
3. **Helm major version pinning** — `helm = "latest"` might upgrade you from v3 to v4 unexpectedly. Pin to the major version (`helm = "4"`) to control upgrades.
4. **Merged kubeconfig syndrome** — tools like `gcloud`, `aws eks update-kubeconfig`, and cluster setup scripts often merge into `~/.kube/config`. Put new configs in `~/.kube/configs/<name>.yaml` instead and avoid the merge bloat.

## Verification Checklist

- [ ] `kubectl version --client` succeeds
- [ ] `helm version` succeeds
- [ ] `kubie version` succeeds
- [ ] `kubeseal version` succeeds
- [ ] `helm plugin list` shows `diff` plugin (if you use it)
- [ ] `~/.kube/configs/` exists with at least one config file
- [ ] `KUBECONFIG` is not set (or points to a specific file, not interfering with kubie)
