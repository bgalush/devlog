---
name: devlog
description: "Creates and maintains repository devlogs: ADRs for decisions, PLANs for tracking work, DIGs for research, GAPs for unaddressed problems or ideas. Use when recording architecture, design rationale, implementation plans, progress, findings, investigations, debugging, root cause analysis, bugs, rough edges, feature ideas, or other devlog updates. Consult in Plan mode when work spans multiple phases, architectural decisions, or substantive investigations."
---

# Devlog

Keep durable engineering records in a repository-local `devlog/`:

| Type | Directory | Create when |
| :--- | :--- | :--- |
| ADR | `devlog/adr/` | Decision has lasting architectural, security, data-model, API, or operational impact; trade-offs matter to future maintainers; reversal is costly. |
| PLAN | `devlog/plan/` | Work spans multiple phases, subsystems, or releases; requires high-level progress tracking beyond individual issues. |
| DIG | `devlog/dig/` | A spike; Researching feasibility, root cause, complex debugging, issue triage, option comparisons, test runs. Too substantial for an ADR footnote, or requires durable evidence/sources. |
| GAP | `devlog/gap/` | An unaddressed bug, trace, rough edge, tech debt, or feature idea; captures problems, friction, or "what-ifs" without derailing current work or committing to action. |

Records use 3-digit numbering and kebab-case titles: `devlog/<type>/NNN-title.md`.

```text
devlog/adr/NNN-title.md
devlog/plan/NNN-title.md
devlog/dig/NNN-title.md
devlog/gap/NNN-title.md
devlog/artifacts/*
```

Sequences are independent per type and local to the repository. Start at `001` and increment from the highest existing number in that directory; never reuse retired or superseded numbers.

## Header Fields and Links

When referencing records in prose, use the hyphenated identifier everywhere (`ADR-012`, `PLAN-008`, `DIG-003`, `GAP-005`) and link every reference with a relative path:

```markdown
[ADR-012](../adr/012-storage-layout.md)
```

Records carry metadata and relative links in their header block:

| Field | Description |
| :--- | :--- |
| `Status:` | Required lifecycle state from the record type's table below. |
| `Release:` | Set to `Unreleased` when created; set once to `vX.Y.Z` when shipped in a tagged release. Never overwrite an existing version tag. |
| `Related:` | Non-ordering context, especially the ADR/PLAN/DIG/GAP records for one initiative. |
| `Depends On:` | Prerequisite decisions or work that must hold first. |
| `Supersedes:` / `Superseded By:` | Revision of prior work, revised architecture, or new research. The reverse link belongs on the replaced record. |

## ADR

Architecture Decision Records document lasting choices. Create an ADR *and* a PLAN when a decision drives a substantial implementation: the ADR captures context and rationale; the PLAN tracks execution and progress towards a goal. ADRs are often justified using research documented in DIGs. Link related records via the `Related:` field.

**Template:** `<skill>/templates/adr/000-template.md`

| Status | Description |
| :--- | :--- |
| `Proposed` | Under consideration |
| `Accepted` | Decided, not yet implemented |
| `Declined` | Not accepted or related to cancelled work |
| `Implemented` | Shipped and representative of the system |
| `Superseded` | Replaced by a newer ADR (requires `Superseded By:`) |

## PLAN

Plans track the progress of a high-level body of work, often associated with one or more ADRs. Do not create a PLAN to track a DIG, unless the research is substantial or requires supplementary work.

**Template:** `<skill>/templates/plan/000-template.md`

| Status | Description |
| :--- | :--- |
| `Not Started` | Work has not yet begun |
| `In Progress` | Work is currently being performed |
| `Complete` | Goal has been reached |
| `Abandoned` | Stopped intentionally without completion |
| `Superseded` | Replaced by a newer PLAN (requires `Superseded By:`) |

## DIG

DIGs capture research questions and findings. Do not create a new DIG for one-off web searches or simple data collection. When collecting research, prefer:

1. **Links:** Cite external sources (URLs, papers, tickets, links to data) in `## Sources` instead of vendoring them.
2. **Reproducible Steps:** Record the commands or procedure that reproduce the data or result. Prefer small `uv run`-able scripts with PEP 723 inline dependencies.
3. **Project-External Storage:** Use the object storage, data store, or dataset registry the project already relies on, and reference the exact location in the DIG.

Research may generate artifacts such as JSON or CSV blobs, screenshots, PDFs, visualizations, scripts, documentation, test results, or raw notes. Avoid committing large or binary files; if these absolutely must be committed, put them in the part of the repository that owns that kind of file, or under `devlog/artifacts/`. List any repository files derived from or depended on by the DIG in its `artifacts` YAML frontmatter; omit the frontmatter when there are none.

**Template:** `<skill>/templates/dig/000-template.md`

| Status | Description |
| :--- | :--- |
| `Open` | Not yet started, or is in progress |
| `Closed` | Research concluded, results recorded |

## GAP

GAPs are a zero-commitment intake buffer for bugs, stack traces, rough edges, tech debt, and feature ideas ("what-ifs"). Open a GAP to park an unvetted idea, or create one proactively when you hit out-of-scope bugs, confusing behavior, or friction during other work. Record the observation and raw context, then keep moving.

Some GAPs are self-contained and can be addressed directly; once fixed, record the outcome in `## Resolution` and set the status to `Resolved`. When a GAP requires deeper investigation, a design decision, or multi-phase work, do not use it as a parallel work tracker—create a `DIG`, `ADR`, or `PLAN`, set the GAP's status to `Promoted`, and link both records via `Related:`.

**Template:** `<skill>/templates/gap/000-template.md`

| Status | Description |
| :--- | :--- |
| `Open` | Encountered and recorded; unaddressed |
| `Promoted` | Handed off to a `DIG`, `ADR`, or `PLAN` (requires `Related:`) |
| `Resolved` | Addressed directly without a separate `DIG`, `ADR`, or `PLAN` |
| `Closed` | Not reproducible, obsolete, or decided not to pursue |

## Verify

- [ ] `NNN` was rechecked against filenames and headings immediately before writing.
- [ ] Filename, H1, and every reference use the same identifier, linked by relative path.
- [ ] Status is one value from the documented lifecycle and matches reality.
- [ ] Supersessions and amendments are linked both ways.
- [ ] Preserve history: Do not rewrite an old record as though the new state had always been true.
