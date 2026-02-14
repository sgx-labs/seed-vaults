---
title: "How Do I Optimize My GitHub Repository for Discovery?"
tags: [github, seo, discovery, optimization, metadata, topics, social-preview]
content_type: research
domain: engineering
---

# How Do I Optimize My GitHub Repository for Discovery?

## The Question

Your project exists on GitHub, but nobody can find it. How do you optimize your repository for discoverability through GitHub search, Google, and social sharing?

## Repository Metadata

### Description
The one-line description appears in GitHub search results, Google snippets, and social previews.

**Good:** "Local-first persistent memory for AI coding agents. Works offline."
**Bad:** "A tool I made" or "WIP - do not use yet"

Keep it under 100 characters. Include your primary keyword.

### Topics (Tags)
Add 5-15 relevant topics. These power GitHub's Explore and Topic pages.

Tips:
- Use existing popular topics (check autocomplete)
- Include language topics (go, python, javascript)
- Include domain topics (cli, developer-tools, ai)
- Include action topics (automation, search, memory)
- Check what similar projects use

### Social Preview Image
A 1280x640px image that appears in link previews on Twitter/X, Discord, Slack.

What to include:
- Project name (large, readable)
- One-line description
- Optional: logo, screenshot, or key visual
- Use high contrast colors (it will be displayed at small sizes)

Tools: Canva (free), Figma, or a simple screenshot of your tool in action.

## README Optimization

### For Google Search
GitHub READMEs are indexed by search engines. Optimize:
- Include keywords naturally in the first paragraph
- Use descriptive headers (H2, H3) with keywords
- Alt text on all images
- Internal links to other repo files

### For GitHub Search
GitHub's search indexes README content, file names, and code. Ensure:
- Key terms appear in your README
- File names are descriptive (not `utils.js` or `helpers.go`)
- Package names are searchable

## Repository Structure Signals

These signal project quality to visitors:

| Signal | What It Tells Visitors |
|--------|----------------------|
| Green CI badge | Tests pass, project is maintained |
| Recent commits | Project is alive |
| Multiple contributors | Community exists |
| Issues with responses | Maintainer is engaged |
| Releases tab populated | Versioned, stable releases |
| License file present | Safe to use |
| CONTRIBUTING.md | Open to contributions |

## Issue Templates and PR Template

Set up at `.github/`:
```
.github/
├── ISSUE_TEMPLATE/
│   ├── bug_report.yml
│   └── feature_request.yml
├── PULL_REQUEST_TEMPLATE.md
└── FUNDING.yml
```

GitHub auto-detects these and presents forms to users.

## Quick Wins Checklist

- [ ] Description filled in (under 100 chars)
- [ ] 5-15 topics added
- [ ] Social preview image set
- [ ] License file present
- [ ] README badge for CI status
- [ ] At least 1 release published
- [ ] Discussions enabled
- [ ] Issue templates configured
- [ ] FUNDING.yml added (if accepting sponsors)

## Related

- `entities/github.md` — GitHub features overview
- `research/docs/readme-that-converts.md` — README best practices
- `hub/launch.md` — Pre-launch checklist
