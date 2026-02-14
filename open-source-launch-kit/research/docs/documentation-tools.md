---
title: "What Documentation Tools Should I Use?"
tags: [documentation, tools, static-site, hosting, docs, docusaurus, mkdocs, vitepress]
content_type: research
domain: engineering
---

# What Documentation Tools Should I Use?

## The Question

When does your project need more than a README, and what tools should you use to build a documentation site?

## When You Need a Docs Site

Stay with README-only when:
- Your project has fewer than 10 configuration options
- Usage fits in 3-4 code examples
- You have fewer than 100 users

Build a docs site when:
- Your README exceeds 300 lines
- Users keep asking the same questions
- You have multiple guides, tutorials, or API references
- You want versioned documentation for different releases

## Documentation Site Generators

### For Most Projects: Use What Matches Your Stack

| Tool | Language | Best For | Hosting |
|------|----------|----------|---------|
| **Docusaurus** | React/JS | Feature-rich docs with versioning | GitHub Pages, Vercel |
| **MkDocs + Material** | Python | Clean, fast, great search | GitHub Pages |
| **VitePress** | Vue/JS | Lightweight, fast builds | GitHub Pages, Netlify |
| **mdBook** | Rust | Rust ecosystem, simple | GitHub Pages |
| **Nextra** | Next.js | Docs + blog combined | Vercel |
| **Starlight** | Astro | Modern, fast, i18n built-in | Any static host |

### Simplest Path (Recommended for Starting Out)

1. Create a `docs/` folder in your repo
2. Write Markdown files
3. Enable GitHub Pages from `docs/` directory
4. Done. No build step, no tooling, no hosting cost.

Upgrade to a static site generator when the simple approach stops working.

## Free Hosting Options

- **GitHub Pages** — Free for public repos. Deploy from a branch or GitHub Actions.
- **Netlify** — Free tier. Automatic deploys from git. Preview deploys for PRs.
- **Vercel** — Free tier. Great for Next.js/Docusaurus. Automatic deploys.
- **Cloudflare Pages** — Free tier. Fast global CDN.

## Documentation Structure

```
docs/
├── index.md              # Landing page
├── getting-started.md    # Installation + first use
├── guides/
│   ├── configuration.md
│   ├── advanced-usage.md
│   └── migration.md      # Version upgrade guide
├── reference/
│   ├── api.md
│   ├── cli.md
│   └── config-options.md
└── contributing.md        # Dev setup for contributors
```

## Documentation Principles

1. **Every page should answer one question** — "How do I configure X?"
2. **Code examples must work** — Test them as part of CI if possible
3. **Link liberally** — Cross-reference related pages
4. **Version your docs** — Users on v1 should not see v2 docs
5. **Search is critical** — Choose a tool with built-in search

## Related

- `hub/docs.md` — Documentation strategy overview
- `research/docs/readme-that-converts.md` — README best practices
- `research/docs/api-documentation.md` — API reference docs
