---
title: "What Is Semantic Versioning and When Do I Bump What?"
tags: [semver, versioning, releases, breaking-changes]
content_type: research
domain: engineering
---

# What Is Semantic Versioning and When Do I Bump What?

## The Question

When you release a new version, how do you decide whether it is 1.2.4 or 1.3.0 or 2.0.0?

## The Rule: MAJOR.MINOR.PATCH

Given a version number MAJOR.MINOR.PATCH:

| Bump | When | Example |
|------|------|---------|
| **PATCH** (1.2.3 -> 1.2.4) | Bug fixes only. No new features. No API changes. | Fixed a crash, corrected typo in output |
| **MINOR** (1.2.3 -> 1.3.0) | New features that are backward compatible. Existing code still works. | Added a new command, new optional flag |
| **MAJOR** (1.2.3 -> 2.0.0) | Breaking changes. Existing code may need to change. | Renamed a CLI flag, changed default behavior, removed a feature |

## The 0.x.y Exception

Before 1.0.0, the API is considered unstable:
- 0.x.y: Anything can change at any time
- Minor bumps (0.1.0 -> 0.2.0) may include breaking changes
- Use 0.x.y during active development

**Move to 1.0.0 when:**
- Your API is stable enough that users can depend on it
- You have real users who would be affected by breaking changes
- You are ready to commit to the semver contract

## Decision Examples

| Change | Bump | Why |
|--------|------|-----|
| Fixed a null pointer crash | PATCH | Bug fix, no API change |
| Added `--verbose` flag | MINOR | New feature, backward compatible |
| Renamed `--output` to `--format` | MAJOR | Breaking: existing scripts break |
| Changed default port from 8080 to 3000 | MAJOR | Breaking: existing configs break |
| Added new optional config field | MINOR | Backward compatible, default works |
| Removed deprecated function | MAJOR | Breaking: code using it breaks |
| Performance improvement | PATCH | No API change |
| Added new dependency | MINOR | New functionality implied |

## Pre-Release Versions

For testing before a stable release:

```
2.0.0-alpha.1    # Very early, unstable
2.0.0-beta.1     # Feature complete, testing
2.0.0-rc.1       # Release candidate, final testing
2.0.0            # Stable release
```

## Conventional Commits (Automate Version Bumps)

Use commit message prefixes to signal version bumps:

```
fix: correct null pointer in search      -> PATCH
feat: add --verbose flag to search       -> MINOR
feat!: rename --output to --format       -> MAJOR
```

Tools like `semantic-release` and `release-please` can automate version bumps based on these commit messages.

## Common Mistakes

1. **Bumping MAJOR for non-breaking changes** — Scares users unnecessarily
2. **Not bumping MAJOR for breaking changes** — Breaks user trust
3. **Staying at 0.x forever** — Signals instability even when the project is stable
4. **Calendar versioning for libraries** — CalVer does not communicate API stability

## Related

- `decisions/log.md` — AD-002 on semver from day one
- `research/releases/handling-breaking-changes.md` — Communicating breaking changes
- `research/releases/writing-changelogs.md` — Documenting what changed
