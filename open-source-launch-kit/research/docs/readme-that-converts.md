---
title: "How Do I Write a README That Converts Visitors to Users?"
tags: [readme, documentation, marketing, conversion, first-impression, seo, landing-page]
content_type: research
domain: engineering
---

# How Do I Write a README That Converts Visitors to Users?

## The Question

Your README is your landing page. A visitor spends 30 seconds deciding whether to try your project or close the tab. How do you make those 30 seconds count?

## The 30-Second Test

A visitor who lands on your README should be able to answer these three questions in 30 seconds:

1. **What is this?** — One sentence, no jargon.
2. **Why should I care?** — What problem does it solve?
3. **How do I start?** — An install command they can copy-paste.

If your README fails this test, nothing else matters.

## Structure That Converts

### Above the Fold (What They See First)

```markdown
# Project Name

One-sentence description of what this does.

[Optional: screenshot/GIF showing it in action]

## Quick Start

npm install -g your-project
your-project init
```

This section alone should be enough to get someone to try your project.

### The Why Section

Do not describe features. Describe the problem you solve.

**Bad:** "A high-performance event-driven framework with middleware support"
**Good:** "Build web APIs in half the code. No boilerplate, no configuration files."

### Usage Examples

Show 2-3 real examples. Every code block must be copy-paste ready.

```markdown
## Usage

# Create a new project
your-tool create my-app

# Run in development mode
your-tool dev

# Deploy to production
your-tool deploy
```

### Feature List

5-8 bullet points maximum. Lead with the benefit, not the implementation.

**Bad:** "Uses SQLite with WAL mode and FTS5 indexing"
**Good:** "Instant full-text search across all your notes — works offline"

## Visual Elements

- **Screenshots or GIFs** — Show your tool in action. A 10-second GIF is worth 500 words.
- **Architecture diagrams** — For complex projects, a simple diagram helps.
- **Badges** — Keep to 3-5: CI status, license, version, downloads. More than 5 looks cluttered.

## What to Cut

- Long backstory (save it for your blog)
- Every configuration option (link to docs instead)
- Detailed API reference (belongs in a separate file)
- Comparison tables with competitors (put in a separate doc)
- More than 300 lines total

## Common README Mistakes

1. **Wall of text** — Use headers, bullets, and whitespace generously
2. **No install command** — The biggest conversion killer
3. **Broken install instructions** — Test on a clean machine
4. **No visual** — Screenshots convert better than descriptions
5. **Marketing speak** — "Blazingly fast" and "revolutionary" repel developers
6. **Outdated content** — Update the README when features change

## README as SEO

GitHub READMEs are indexed by search engines. Include:
- Natural keywords that someone would search for
- Your project name and what it does in the first paragraph
- Descriptive section headers
- Alt text on images

## Related

- `templates/readme-template.md` — Fill-in README template
- `hub/docs.md` — Full documentation strategy
- `research/launch/minimum-viable-oss-project.md` — MVP project structure
