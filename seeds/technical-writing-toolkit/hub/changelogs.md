---
title: "Changelogs That People Actually Read"
tags: [changelog, release-notes, versioning, semver, keep-a-changelog]
content_type: hub
domain: technical-writing
---

# Changelogs That People Actually Read

## Why Changelogs Matter

A changelog answers the question every user has after an update: "What changed and do I need to do anything?" If your changelog doesn't answer that clearly, people stop reading it -- and then they miss breaking changes.

## Keep a Changelog Format

The [Keep a Changelog](https://keepachangelog.com) format is the closest thing to a standard. Use it.

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com),
and this project adheres to [Semantic Versioning](https://semver.org).

## [Unreleased]

### Added
- WebSocket support for real-time notifications

## [2.1.0] - 2025-03-10

### Added
- Cursor-based pagination on /users and /orders endpoints
- Rate limit headers on all responses

### Changed
- Default page size increased from 20 to 50

### Fixed
- 500 error when filtering users by email containing a plus sign

## [2.0.0] - 2025-02-01

### Changed
- **BREAKING:** Authentication switched from API keys to OAuth2
  - Migration guide: docs/migration-v2.md

### Removed
- **BREAKING:** Removed /v1/ endpoints (deprecated since 1.8.0)
```

## The Categories

- **Added** -- new features
- **Changed** -- changes to existing functionality
- **Deprecated** -- features that will be removed in a future version
- **Removed** -- features that were removed
- **Fixed** -- bug fixes
- **Security** -- vulnerability fixes

## Before and After

**Don't write this:**

```
v2.1.0
- Various improvements and bug fixes
- Updated dependencies
- Performance improvements
```

**Write this instead:**

```
## [2.1.0] - 2025-03-10

### Fixed
- CSV export no longer truncates rows after 10,000 entries
- Search no longer returns deleted items

### Changed
- Dashboard loads 40% faster (lazy-loaded charts)
```

## Semantic Versioning Tie-In

Link your changelog to [semver](https://semver.org):

- **MAJOR** (3.0.0) -- breaking changes. Always explain what breaks and how to migrate.
- **MINOR** (2.1.0) -- new features, backwards compatible. Highlight what's new.
- **PATCH** (2.0.1) -- bug fixes only. List what was broken and what's fixed.

## Writing Tips

- **Lead with impact, not implementation.** "CSV export handles files over 10K rows" beats "Fixed buffer overflow in csv.go line 247."
- **Call out breaking changes loudly.** Bold them. Add migration guides.
- **Keep an [Unreleased] section.** Add entries as you merge PRs, not at release time.
- **Link to issues or PRs.** Gives context without cluttering the changelog.
- **Date every release.** ISO 8601 format: YYYY-MM-DD.

## Automation

Don't write changelogs from memory at release time. Options:

- **Conventional Commits** -- structured commit messages that tools can parse
- **PR labels** -- label PRs as `feature`, `bugfix`, `breaking`, auto-generate changelog
- **Manual [Unreleased]** -- add to the changelog when you merge, release when you tag

The best approach: add entries as you go, auto-format at release time.

## See Also

- `hub/api-documentation.md` -- Changelogs as part of API docs
- `hub/rfcs-and-design-docs.md` -- Design docs that precede changelog entries
- `guides/writing-release-notes.md` -- Release notes vs changelogs
- `entities/common-documentation-templates.md` -- Changelog template
