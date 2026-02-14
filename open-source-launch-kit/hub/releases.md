---
title: "Releases — Versioning, Changelogs, CI/CD, and Distribution"
tags: [releases, versioning, cicd, changelog, distribution, semver, automation]
content_type: hub
domain: engineering
---

# Releases — Versioning, Changelogs, CI/CD, and Distribution

## Overview

A solid release process covers semantic versioning, Keep a Changelog formatting, GitHub Actions CI/CD automation, and multi-platform binary distribution through npm, Homebrew, and GitHub Releases. This hub walks through the complete release workflow from deciding what ships and writing user-facing changelogs, to tagging, building artifacts, publishing packages, and writing release notes that communicate breaking changes and migration steps clearly.

## Release Workflow

### 1. Decide What Ships
- Review merged PRs and closed issues since last release.
- Categorize changes: features, fixes, breaking changes.
- Determine version bump (see `research/releases/semantic-versioning.md`).

### 2. Update Changelog
- Add entries under the new version heading.
- Follow Keep a Changelog format (see `research/releases/writing-changelogs.md`).
- Write for humans, not robots — explain WHY, not just what.

### 3. Tag and Release
- Create a git tag: `git tag -a v1.2.0 -m "Release v1.2.0"`
- Push the tag: `git push origin v1.2.0`
- CI builds artifacts and creates GitHub Release automatically.

### 4. Distribute
- npm publish (see `entities/npm.md`)
- Homebrew formula update (see `entities/homebrew.md`)
- Binary uploads to GitHub Releases
- Docker image push if applicable

### 5. Announce
- GitHub Release notes (auto-generated + edited)
- Discord/community channel
- Social media if significant release

## Version Strategy

| Change Type | Version Bump | Example |
|------------|-------------|---------|
| Bug fix, no API change | PATCH | 1.2.3 -> 1.2.4 |
| New feature, backward compatible | MINOR | 1.2.3 -> 1.3.0 |
| Breaking change | MAJOR | 1.2.3 -> 2.0.0 |
| Pre-release | Pre-release tag | 1.3.0-beta.1 |

## Research Notes

- `research/releases/semantic-versioning.md` — When to bump what
- `research/releases/writing-changelogs.md` — Changelogs people actually read
- `research/releases/github-actions-ci.md` — CI/CD setup for automated releases
- `research/releases/handling-breaking-changes.md` — Communicating breaking changes
- `research/releases/release-notes.md` — Writing effective release notes
- `research/releases/binary-distribution.md` — npm, Homebrew, and binary distribution

## See Also

- `hub/docs.md` — Migration guides and documentation for major versions
- `hub/launch.md` — First release as part of your launch sequence
- `hub/marketing.md` — Release announcements as marketing opportunities
- `hub/community.md` — Contributor recognition in release notes
- `hub/sustainability.md` — Automating releases to reduce maintenance burden
- `templates/changelog-template.md` — Changelog format template
- `entities/npm.md` — npm publishing and trusted publishing setup
- `entities/homebrew.md` — Homebrew tap and formula automation
