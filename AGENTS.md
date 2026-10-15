# AGENTS.md — devstack (`bgalush/devstack`)

## Purpose

Canonical repository for general engineering workflow skills (`devlog`, `changelog`) and reproducible development / language preference skills (`development-environment-setup`, `preferences-{go,rust,typescript,python,kubernetes,shell}`).

## Structure

Every skill lives in `skills/<skill-name>/SKILL.md` (with optional `templates/` or `references/` subdirectories) and is discovered automatically by the Vercel `skills` CLI:

```bash
bunx --bun skills add bgalush/devstack --list
```

Lab-specific infrastructure and inference skills do **not** belong in this repository; they live in `homelab/agents` and `homelab/swapsite`.
