---
title: "Documentation Tooling Landscape"
tags: [tooling, docusaurus, mintlify, gitbook, starlight, nextra, static-site, documentation]
content_type: research
domain: technical-writing
---

# Documentation Tooling Landscape

## When You Need a Tool

Plain Markdown files in a repo work for small projects. You need a documentation tool when:
- You have more than ~30 pages
- You need search across docs
- You need versioned docs for multiple releases
- You want a polished public-facing site
- Multiple non-technical contributors need to edit

## The Options

### Docusaurus

**Best for:** Open source projects, developer documentation with versioning needs.

- React-based static site generator by Meta
- Built-in versioning, search, i18n
- MDX support (Markdown + React components)
- Large community, well-documented
- **Tradeoff:** Heavier setup, React knowledge helps for customization

### Starlight (Astro)

**Best for:** Fast, lightweight documentation sites with minimal config.

- Built on Astro -- fast by default, ships minimal JavaScript
- Excellent defaults out of the box
- Built-in search, sidebar generation, i18n
- Supports Markdown and MDX
- **Tradeoff:** Newer, smaller ecosystem than Docusaurus

### Mintlify

**Best for:** API documentation with a polished look, minimal setup.

- Hosted service with Git-based workflow
- Beautiful default theme (especially for API docs)
- Built-in API playground, OpenAPI integration
- Auto-generates from OpenAPI specs
- **Tradeoff:** Vendor lock-in, paid for advanced features

### GitBook

**Best for:** Teams that want a wiki-like editing experience with version control.

- WYSIWYG editor with Git sync
- Good for mixed technical/non-technical teams
- Spaces and collections for organization
- **Tradeoff:** Free tier is limited, less customizable than static site generators

### Nextra

**Best for:** Next.js projects that want docs alongside their app.

- Built on Next.js -- shares the same stack
- Simple, minimal configuration
- Good Markdown support
- **Tradeoff:** Tied to the Next.js ecosystem

### MkDocs (Material)

**Best for:** Python projects, teams that want simplicity.

- Python-based, simple YAML config
- Material theme is polished and feature-rich
- Excellent search, navigation, and code highlighting
- **Tradeoff:** Python ecosystem, less flexible than React-based tools

## Comparison

| Tool | Language | Hosting | Versioning | Search | API Docs |
|------|----------|---------|-----------|--------|----------|
| Docusaurus | React | Self/any | Built-in | Algolia/local | Plugin |
| Starlight | Astro | Self/any | Manual | Built-in | Plugin |
| Mintlify | N/A | Hosted | Built-in | Built-in | Built-in |
| GitBook | N/A | Hosted | Built-in | Built-in | Limited |
| Nextra | Next.js | Self/any | Manual | Built-in | Manual |
| MkDocs | Python | Self/any | mike plugin | Built-in | Plugin |

## How to Choose

1. **Are you an open source project?** Docusaurus or Starlight.
2. **Do you need API docs with interactive playground?** Mintlify or Docusaurus + plugin.
3. **Is your team mostly non-technical?** GitBook.
4. **Do you want the fastest setup?** Starlight.
5. **Are you already using Next.js?** Nextra.
6. **Are you a Python project?** MkDocs Material.
7. **Do you need zero vendor lock-in?** Any static site generator.

## The Best Tool Is the One You'll Use

A simple `docs/` directory with Markdown files and no build step beats an abandoned Docusaurus site every time. Start simple. Add tooling when the pain of not having it exceeds the cost of setting it up.

## See Also

- `research/documentation-as-code.md` -- The workflow these tools support
- `hub/api-documentation.md` -- API documentation specifically
- `decisions/documentation-tool-choice.md` -- Template for choosing a tool
- `entities/writing-tools-reference.md` -- Linting and quality tools
