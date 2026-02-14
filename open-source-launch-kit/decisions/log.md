---
title: "Decision Log"
tags: [decisions, architecture, log]
content_type: decision
domain: engineering
---

# Decision Log

## AD-001: MIT License unless strong reason otherwise

**Status:** Accepted
**Date:** 2026-02-14

MIT License for maximum adoption and minimum friction. It is the most widely understood open source license. Contributors and companies know exactly what it means. No patent clauses to worry about, no copyleft obligations to track.

**When to reconsider:** If you need patent protection (use Apache 2.0) or want to prevent proprietary forks (use AGPL). See `research/launch/choosing-a-license.md`.

## AD-002: Semantic versioning from day one

**Status:** Accepted
**Date:** 2026-02-14

Follow semver (MAJOR.MINOR.PATCH) from the first public release. Users need to trust that a patch update will not break their code. Breaking that trust — even once — damages adoption permanently. Start at 0.1.0 during development, move to 1.0.0 when API stabilizes.

**Alternative considered:** Calendar versioning (CalVer). Rejected for most projects because it does not communicate API stability.

## AD-003: GitHub Actions for CI

**Status:** Accepted
**Date:** 2026-02-14

GitHub Actions provides free, unlimited CI minutes for public repositories. The ecosystem of pre-built actions is massive. Workflows live in `.github/workflows/` alongside your code. No external CI service to configure, no additional account to manage.

**Alternative considered:** CircleCI, Travis CI, GitLab CI. All viable but add external dependencies. GitHub Actions keeps everything in one place.

## AD-004: CHANGELOG.md follows Keep a Changelog format

**Status:** Accepted
**Date:** 2026-02-14

Use the Keep a Changelog format with sections: Added, Changed, Deprecated, Removed, Fixed, Security. It is structured, parseable by tools, and readable by humans. Version entries include the release date in YYYY-MM-DD format. An `[Unreleased]` section at the top collects changes between releases.

**Alternative considered:** Auto-generated changelogs from commit messages. Rejected because commit messages are written for developers, not users. Changelogs should be curated and human-readable.
