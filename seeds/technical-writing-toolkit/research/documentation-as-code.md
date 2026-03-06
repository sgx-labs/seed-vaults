---
title: "Documentation as Code"
tags: [docs-as-code, version-control, ci, linting, pull-requests, documentation]
content_type: research
domain: technical-writing
---

# Documentation as Code

## The Idea

Treat documentation the same way you treat code: store it in the repo, review it in pull requests, test it in CI, and version it with releases. When docs live next to the code they describe, they're more likely to stay current.

## Why It Works

- **Proximity.** Docs in the repo get updated in the same PR as the code change. Docs in a wiki get forgotten.
- **Review.** Pull request reviews catch doc errors the same way they catch code errors.
- **History.** Git blame shows who wrote each line and why. You can trace doc changes to the commits that motivated them.
- **Testing.** CI can lint Markdown, check links, validate examples, and ensure docs build.
- **Versioning.** Docs for v1 live on the v1 branch. No more "which wiki page is current?"

## What to Put in the Repo

| In the repo | In a wiki or external tool |
|------------|---------------------------|
| README, CONTRIBUTING | Meeting notes |
| API references | Product specs |
| ADRs | Team processes |
| Changelogs | Vendor comparisons |
| Runbooks | Org-wide policies |
| Code comments | Customer-facing help center |
| Setup guides | |

**Rule of thumb:** If the doc describes code or developer workflow, it belongs in the repo. If it describes people or process, it probably belongs elsewhere.

## CI Checks for Docs

Add these to your pipeline:

```yaml
# Example: GitHub Actions
docs-check:
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v4

    - name: Lint Markdown
      uses: DavidAnson/markdownlint-cli2-action@v16

    - name: Check links
      uses: lycheeverse/lychee-action@v1
      with:
        args: "docs/ README.md"

    - name: Check spelling
      uses: streetsidesoftware/cspell-action@v6
```

Start with link checking and Markdown linting. Add spelling and style checks as your team matures.

## Directory Structure

```
project/
  docs/
    decisions/        # ADRs
    guides/           # How-to guides
    architecture.md   # System overview
    deployment.md     # Deploy procedures
  README.md           # Entry point
  CONTRIBUTING.md     # How to contribute
  CHANGELOG.md        # Release history
  .markdownlint.json  # Linting config
```

## Common Objections

**"Developers won't write docs."** They won't write in a wiki either. But they WILL update a Markdown file in the same PR where they changed the code, especially if CI enforces it.

**"Non-technical people can't edit Markdown."** Most can learn Markdown basics in 10 minutes. For truly non-technical audiences, keep customer-facing docs in a CMS and developer-facing docs in the repo.

**"It's too much overhead."** A `docs/` directory with 5-10 Markdown files and a linter config takes 30 minutes to set up. The time savings from having accurate docs far exceeds the setup cost.

## Pitfalls

- **Docs that don't build.** If you use a static site generator, broken builds block deploys. Keep the doc build simple.
- **Stale doc PRs.** Docs-only PRs get deprioritized. Encourage updating docs in the same PR as the code change.
- **Over-engineering.** You don't need a custom doc framework. Markdown files in a directory work fine until you have 100+ pages.

## See Also

- `hub/internal-wiki-best-practices.md` -- When a wiki is the right choice
- `research/docs-tooling-landscape.md` -- Tools for building doc sites from Markdown
- `hub/changelogs.md` -- Changelogs as docs-as-code
- `hub/architecture-decision-records.md` -- ADRs in the repo
- `entities/writing-tools-reference.md` -- Linting tools
