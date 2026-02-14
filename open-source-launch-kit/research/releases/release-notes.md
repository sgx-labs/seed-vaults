---
title: "How Do I Write Good Release Notes?"
tags: [release-notes, communication, releases, github, announcement, highlights]
content_type: research
domain: engineering
---

# How Do I Write Good Release Notes?

## The Question

Release notes and changelogs serve different purposes. Changelogs are cumulative records. Release notes are announcements. How do you write release notes that get people excited about updating?

## Release Notes vs Changelog

| | Changelog | Release Notes |
|---|-----------|--------------|
| **Audience** | Developers checking specific changes | Everyone deciding whether to update |
| **Tone** | Factual, structured | Narrative, highlights-focused |
| **Length** | As long as needed | 1 page maximum |
| **Location** | CHANGELOG.md | GitHub Releases page |
| **Updates** | Every change | Only significant releases |

## Structure of Great Release Notes

### 1. Headline: What Is the Story of This Release?

Not "v1.3.0 released." Instead: "v1.3.0: Federated search is here"

Lead with the most exciting change. One sentence that makes someone want to read more.

### 2. Highlights (3-5 Items)

The changes that matter most to users. Include a brief explanation and, where possible, a screenshot or code example.

```markdown
## Highlights

### Federated Search
Search across multiple vaults simultaneously with `search --all`.
Results are merged and ranked by relevance.

### 3x Faster Indexing
Rewritten the indexing pipeline to process notes in parallel.
A 500-note vault now indexes in under 2 seconds.

### Better Error Messages
Every error now includes a suggested fix and a link to
the relevant documentation.
```

### 3. Full Changelog Link

Link to the complete changelog for people who want details:

```markdown
## Full Changelog
See [CHANGELOG.md](CHANGELOG.md#130---2026-02-14) for all changes.
```

### 4. Breaking Changes (If Any)

Always put breaking changes in a prominent section with migration instructions:

```markdown
## Breaking Changes

- The `--output` flag has been renamed to `--format`.
  See the [migration guide](docs/migration-v2-to-v3.md).
```

### 5. Thank Contributors

```markdown
## Contributors
Thanks to @contributor1, @contributor2, and @contributor3
for their contributions to this release.
```

## GitHub Releases Automation

GitHub can auto-generate release notes from merged PRs:

1. Push a tag: `git tag -a v1.3.0 -m "v1.3.0"`
2. Go to Releases > Draft new release
3. Select the tag
4. Click "Generate release notes"
5. Edit the auto-generated content to add narrative

Or automate in your release workflow:
```yaml
- uses: softprops/action-gh-release@v2
  with:
    generate_release_notes: true
```

## Writing Tips

- **Lead with benefits, not features** — "Search is faster" not "Refactored search internals"
- **Include numbers** — "3x faster" is more compelling than "faster"
- **Show, do not tell** — Screenshots, GIFs, code examples
- **Be honest about limitations** — "Known issue: X does not work on Windows yet"
- **Keep it under 1 page** — Link to detailed docs for deep dives

## Related

- `research/releases/writing-changelogs.md` — Changelog format and practices
- `hub/releases.md` — Full release workflow
- `entities/github.md` — GitHub Releases features
