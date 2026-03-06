---
title: "Migrating from Wiki to Docs-as-Code"
tags: [guide, migration, wiki, docs-as-code, transition, documentation]
content_type: guide
domain: technical-writing
---

# Migrating from Wiki to Docs-as-Code

## Before You Start

This guide assumes you have documentation in a wiki (Confluence, Notion, Google Docs) and want to move developer-facing docs into your Git repository.

**Don't move everything.** Only technical documentation belongs in the repo. Team processes, meeting notes, and product specs can stay in the wiki.

## Step 1: Audit Your Wiki (1-2 hours)

Export a list of all pages. Categorize each:

| Category | Action | Example |
|----------|--------|---------|
| Technical, current | Move to repo | Setup guide, API docs |
| Technical, outdated | Update and move | Old architecture doc |
| Non-technical | Keep in wiki | Meeting notes, HR policies |
| Redundant | Archive | Duplicates, outdated drafts |
| Unused | Delete | Pages with zero views |

## Step 2: Create the Repo Structure (30 minutes)

```
docs/
  getting-started.md
  architecture.md
  guides/
    deploying.md
    adding-endpoints.md
  decisions/
    001-use-postgres.md
  runbooks/
    restart-service.md
README.md
CONTRIBUTING.md
CHANGELOG.md
```

## Step 3: Convert Content (Largest Time Investment)

For each page moving to the repo:

1. **Copy the content** to a new Markdown file
2. **Clean up formatting** -- wiki syntax to Markdown, fix broken links
3. **Update for accuracy** -- don't migrate stale content
4. **Add YAML frontmatter** if using SAME or a doc tool
5. **Add cross-references** to related docs

Work in batches. Don't try to migrate everything in one day.

### Common Conversion Issues

| Wiki feature | Markdown equivalent |
|-------------|-------------------|
| Rich text formatting | Markdown formatting |
| Embedded images | Image files in repo + Markdown links |
| Page hierarchy | Directory structure |
| Comments | Not applicable (use PR reviews) |
| Inline tables | Markdown tables (simpler) |
| Macros/widgets | Remove or replace with text |

## Step 4: Set Up CI (1 hour)

Add basic checks from day one:

```yaml
name: Docs
on: [pull_request]
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: DavidAnson/markdownlint-cli2-action@v16
      - uses: lycheeverse/lychee-action@v1
        with:
          args: "docs/ README.md"
```

## Step 5: Redirect and Archive (30 minutes)

After migrating a page:
1. Replace the wiki page content with a link to the repo doc
2. Mark the wiki page as "Moved to repo" with the file path
3. Keep the wiki page for 3 months as a redirect, then archive

## Step 6: Change the Workflow

The biggest change isn't the content -- it's the habit. Enforce the new workflow:

- Documentation changes go in PRs alongside code changes
- Review docs in code review
- Point questions to repo docs, not the wiki
- Archive wiki pages that have been migrated

## Timeline

| Week | Activity |
|------|----------|
| 1 | Audit wiki, create repo structure, migrate README and setup docs |
| 2-3 | Migrate remaining technical docs in batches |
| 4 | Set up CI checks, redirect wiki pages |
| Ongoing | Enforce new workflow, archive old wiki pages after 3 months |

## Common Pitfalls

- **Moving everything at once.** Migrate in phases. Start with the most-used docs.
- **Keeping the wiki "just in case."** This creates two sources of truth. Set a deadline for deprecation.
- **Not updating migrated content.** Migration is a chance to improve. Don't just copy stale content.
- **Forgetting non-developers.** Some wiki readers can't use Git. Decide how they'll access docs (published site, read-only view).

## See Also

- `research/documentation-as-code.md` -- The philosophy behind this migration
- `research/docs-tooling-landscape.md` -- Building a doc site from repo content
- `hub/internal-wiki-best-practices.md` -- What should stay in the wiki
- `entities/writing-tools-reference.md` -- CI tools for the new workflow
