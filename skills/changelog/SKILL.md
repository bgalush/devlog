---
name: changelog
description: "Maintains repository-local changelogs: a high-level index and detailed versioned records. Use when performing releases, tagging versions, and summarizing completed work, or when exploring historical release notes. Required before finalizing completed work. Intended to complement the `devlog` skill."
---

# Changelog

Maintain a durable, agent-friendly history of project releases in the repository. Structure is two parts: A top-level index (`CHANGELOG.md`) and a directory of detailed versioned logs (`changelogs/vX.Y.Z.md`).

```text
CHANGELOG.md
changelogs/
  v0.1.0.md
  v0.2.0.md
  ...
```

Prefer [Semantic Versioning](https://semver.org/) unless otherwise specified.

## Index

`CHANGELOG.md` is a high-level index. Each entry is a one-line summary of the release and a relative link to the detailed record.

```text
- [v1.1.0](changelogs/v1.1.0.md) - Enhance API security with SigV4 support
```

## Detail

`changelogs/vX.Y.Z.md` contains detailed release notes. The body groups notable changes by domain and links back to the `devlog/` records `ADR-NNN`, `PLAN-NNN`, or `DIG-NNN` that drove the change.

`<skill>/templates/v0.0.0-template.md` is included in the skill root. 

## Workflow

Changelogs are the final output of the engineering record lifecycle. A feature is considered shipped when its corresponding `PLAN` is `Complete` and its highlights are summarized in a versioned changelog.

1. **Draft**: When a version is tagged, create a new `changelogs/vX.Y.Z.md` using the template.
2. **Summarize**: Extract key highlights from completed `PLAN` and `ADR` or `DIG` entries in the `devlog` skill.
3. **Index**: Add the release summary and link to the top of `CHANGELOG.md`.
4. **Devlog**: The `Release:` field in the `ADR`, `PLAN`, or `DIG` should be set to reference the version it was originally released in. Do not rewrite history by updating `Release:` for an existing record.

## Verify

- [ ] The release is tagged in git.
- [ ] `changelogs/vX.Y.Z.md` contains detailed notes grouped by domain.
- [ ] `CHANGELOG.md` index is updated with a one-line summary and link.
- [ ] All entries link back to the relevant `ADR`, `PLAN` or `DIG` in the `devlog/`, and each record's `Release:` is updated.
- [ ] Relative paths are correct and functional.
