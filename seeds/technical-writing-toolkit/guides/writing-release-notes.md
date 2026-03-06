---
title: "Writing Release Notes That People Read"
tags: [guide, release-notes, changelog, communication, documentation]
content_type: guide
domain: technical-writing
---

# Writing Release Notes That People Read

## Release Notes vs Changelogs

A **changelog** is a technical record of every change. It lives in the repo and serves developers.

**Release notes** are a communication tool. They tell users what changed, why it matters to *them*, and what they need to do. They're written for humans, not for git log.

## Step 1: Gather Changes

Start with your changelog or git log since the last release:

```bash
git log v1.2.0..HEAD --oneline
```

Group changes by impact:
- **Breaking changes** -- things users MUST address
- **New features** -- things users will want to know about
- **Notable fixes** -- bugs that affected users
- **Everything else** -- minor fixes, internal improvements (summarize briefly or skip)

## Step 2: Write for Impact, Not Implementation

For each change, translate from developer language to user language.

**Don't write this:**

```
- Fixed buffer overflow in csv.go line 247 when row count exceeds int32 max
- Refactored pagination to use cursor-based approach
- Added --format flag to export command
```

**Write this instead:**

```
## What's New

### Export to more formats
You can now export data as CSV, JSON, or Parquet:
  mytool export --format parquet data.csv

### Large file support
CSV exports now handle files with over 2 billion rows without crashing.

### Faster pagination
List operations are now 10x faster on large datasets. No changes needed
on your end — the improvement is automatic.
```

## Step 3: Highlight Breaking Changes

Breaking changes get top billing, bold formatting, and migration instructions:

```markdown
## Breaking Changes

### Authentication method changed

**What changed:** API key authentication has been replaced with OAuth2.
API keys will stop working in v3.1 (June 2025).

**What to do:**
1. Generate OAuth2 credentials at https://app.example.com/settings/oauth
2. Update your integration to use the new auth flow
3. See the [migration guide](link) for code examples

**Why:** OAuth2 provides better security with scoped permissions
and token rotation.
```

## Step 4: Structure the Release Notes

```markdown
# v2.0.0 Release Notes

*Released March 15, 2025*

[One paragraph summary: what's the theme of this release?]

## Breaking Changes
[List each one with migration instructions]

## New Features
[2-3 highlights with brief examples]

## Improvements
[Notable improvements, briefly]

## Bug Fixes
[Fixes users might have noticed]

## Full Changelog
[Link to the detailed changelog or git comparison]
```

## Step 5: Choose Your Channel

| Channel | Format | Audience |
|---------|--------|----------|
| GitHub Releases | Full release notes | Developers following the repo |
| Blog post | Narrative with screenshots | Broader audience |
| Email | Summary with links | Registered users |
| Changelog file | Technical detail | Developers reading the repo |
| In-app notification | One-line summary | Active users |

Most projects need at least GitHub Releases + Changelog.

## Common Release Note Mistakes

- **Too much detail.** Release notes aren't a commit log. Summarize.
- **No user perspective.** "Refactored internal caching" tells the user nothing. "Dashboard loads 40% faster" tells them everything.
- **Missing breaking changes.** Users who discover breaking changes by surprise lose trust.
- **No migration path.** Telling users something changed without telling them what to do is unhelpful.
- **Delayed notes.** Release notes should ship with the release, not a week later.

## See Also

- `hub/changelogs.md` -- Changelogs as the source material for release notes
- `hub/writing-style-guide.md` -- Clarity and impact in writing
- `hub/api-documentation.md` -- API-specific release communication
- `research/writing-for-different-audiences.md` -- Adapting notes for different readers
