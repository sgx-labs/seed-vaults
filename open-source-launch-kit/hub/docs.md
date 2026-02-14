---
title: "Documentation — Strategy, README, Guides, and API Docs"
tags: [documentation, readme, guides, api-docs, writing, developer-experience]
content_type: hub
domain: engineering
---

# Documentation — Strategy, README, Guides, and API Docs

## Overview

Open source documentation spans four layers: README as landing page, getting started guides for onboarding, API and CLI reference for daily use, and tutorials for power users. This hub covers writing READMEs that convert visitors to users, choosing documentation site generators like Docusaurus or MkDocs, writing copy-paste-friendly install instructions, and documenting APIs and CLI commands with working examples and clear parameter references.

## Documentation Layers

### Layer 1: README (everyone reads this)
Your README has 30 seconds to answer three questions:
1. **What is this?** — One sentence.
2. **Why should I care?** — What problem does it solve?
3. **How do I start?** — Install command + minimal example.

See `research/docs/readme-that-converts.md` and `templates/readme-template.md`.

### Layer 2: Getting Started Guide (new users)
Walk someone from zero to working in under 5 minutes:
- Installation with prerequisites
- Minimal working example
- Common first steps
- "What next?" links

### Layer 3: Reference Docs (regular users)
- API reference with every public function/method
- Configuration options with defaults
- CLI commands with flags and examples
- Error messages and troubleshooting

### Layer 4: Guides and Tutorials (power users)
- How-to guides for specific tasks
- Architecture overview for contributors
- Migration guides for major versions

## Documentation Principles

1. **Show, don't tell** — Code examples beat paragraphs of explanation.
2. **Copy-paste friendly** — Every code block should work if pasted directly.
3. **Keep it updated** — Outdated docs are worse than no docs. Link to versioned docs.
4. **Test your docs** — Run every install command on a clean machine.
5. **Progressive disclosure** — Simple first, detailed later.

## Research Notes

- `research/docs/readme-that-converts.md` — Writing READMEs that turn visitors into users
- `research/docs/documentation-tools.md` — Static site generators and doc hosting
- `research/docs/writing-install-instructions.md` — Installation docs that actually work
- `research/docs/api-documentation.md` — Documenting APIs and CLIs

## See Also

- `hub/launch.md` — Pre-launch documentation checklist and repository essentials
- `hub/community.md` — Good docs reduce community support burden
- `hub/releases.md` — Migration guides and changelogs for major versions
- `hub/marketing.md` — README as your primary marketing asset
- `hub/sustainability.md` — Documentation that enables maintainer succession
- `templates/readme-template.md` — README fill-in template
- `templates/contributing-template.md` — CONTRIBUTING.md fill-in template
