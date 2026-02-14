---
title: "How Do I Write a Changelog That People Actually Read?"
tags: [changelog, releases, documentation, communication]
content_type: research
domain: engineering
---

# How Do I Write a Changelog That People Actually Read?

## The Question

Most changelogs are either unreadable commit dumps or do not exist at all. How do you write one that users actually find useful?

## Use the Keep a Changelog Format

The Keep a Changelog standard provides a consistent, parseable structure:

```markdown
# Changelog

## [Unreleased]

## [1.2.0] - 2026-02-14

### Added
- New `search --all` flag for federated search across vaults

### Changed
- Default search results increased from 5 to 10

### Fixed
- Fixed crash when vault path contains spaces
- Resolved slow startup on first indexing run

## [1.1.0] - 2026-01-20

### Added
- Support for YAML frontmatter in notes
```

## The Categories

| Category | What Goes Here | Example |
|----------|---------------|---------|
| **Added** | New features | "Added `--json` output flag" |
| **Changed** | Changes to existing features | "Increased default timeout to 30s" |
| **Deprecated** | Features that will be removed | "Deprecated `--legacy` flag" |
| **Removed** | Removed features | "Removed support for Node 14" |
| **Fixed** | Bug fixes | "Fixed crash when config is empty" |
| **Security** | Vulnerability fixes | "Updated dependency X to fix CVE-2026-1234" |

## Writing Principles

### Write for Users, Not Developers

**Bad (commit dump):**
```
- refactor: extract search logic to separate module
- chore: update CI workflow
- fix: handle nil pointer in processChunks
```

**Good (user-facing):**
```
### Fixed
- Fixed crash when searching with an empty query
```

### Explain Impact, Not Implementation

**Bad:** "Changed the scoring algorithm in ranking.go"
**Good:** "Search results are now more relevant for short queries"

### Always Mention Breaking Changes

```markdown
### Changed
- **BREAKING:** The `--output` flag has been renamed to `--format`.
  To migrate, replace `--output json` with `--format json` in your scripts.
```

## The [Unreleased] Section

Keep an `[Unreleased]` section at the top. Add entries as you merge PRs, not all at once on release day. This makes releases easier and ensures nothing gets forgotten.

## Automation Options

- **release-please** — Google's tool. Creates release PRs from conventional commits.
- **semantic-release** — Fully automated releases from commit messages.
- **git-cliff** — Generates changelogs from conventional commits.

Recommendation: Even with automation, manually edit the changelog. Automated changelogs read like commit logs. Curate them.

## Related

- `templates/changelog-template.md` — Changelog format template
- `decisions/log.md` — AD-004 on Keep a Changelog format
- `research/releases/semantic-versioning.md` — Version numbering
- `research/releases/release-notes.md` — Release notes (different from changelogs)
