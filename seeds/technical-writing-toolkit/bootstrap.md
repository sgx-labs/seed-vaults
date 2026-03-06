---
title: "Bootstrap — New Session Quick Start"
tags: [bootstrap, onboarding, session-start, water-the-seed, research-cascade, orientation, technical-writing]
content_type: hub
domain: technical-writing
pinned: true
---

# Bootstrap — New Session Quick Start

## Water the Seed

First time? Your agent will handle everything. Just run:

```bash
same init && same reindex
```

Then start a conversation. Your agent reads this file automatically and begins the Research Cascade -- a multi-phase process that scans your project's documentation, identifies gaps, and fills your vault with personalized knowledge. The more you interact, the smarter it gets.

**What happens next (automatic):**
1. Agent silently scans your project -- README, docs/, ADRs, changelogs, code comments, CI config
2. Agent presents an insight report showing your documentation landscape
3. Agent scores you against the documentation maturity model
4. Agent asks 3-5 targeted questions about your docs and pain points
5. Agent proposes CLAUDE.md additions (archives originals safely)
6. Agent recommends research topics specific to YOUR documentation gaps
7. You pick which topics matter -- agents research in parallel, save results to your vault
8. Each research result suggests new topics -- the vault grows exponentially

After the first session, your vault has general writing knowledge PLUS project-specific documentation intelligence.

## Returning Sessions

Already set up? Here is what your agent does automatically on every session:

- **Loads context** -- SAME surfaces your pinned notes, recent handoffs, and decisions
- **Remembers where you left off** -- session handoffs capture what was done and what is pending
- **Searches before generating** -- your vault has 50+ expert notes, growing with every session
- **Cross-references** -- finds related patterns you might not know about
- **Compounds knowledge** -- every useful answer gets saved for next time

## Where To Find Things

| Need | Search for | Or read directly |
|------|-----------|-----------------|
| README help | `README structure quickstart` | `hub/writing-great-readmes.md` |
| API docs | `API documentation OpenAPI` | `hub/api-documentation.md` |
| ADRs | `ADR decision record` | `hub/architecture-decision-records.md` |
| Runbooks | `runbook incident playbook` | `hub/runbooks-and-playbooks.md` |
| Changelogs | `changelog release notes` | `hub/changelogs.md` |
| RFCs | `RFC design doc proposal` | `hub/rfcs-and-design-docs.md` |
| Code comments | `comments self-documenting` | `hub/code-comments-philosophy.md` |
| Error messages | `error message helpful` | `hub/error-messages.md` |
| Onboarding | `onboarding new hire` | `hub/onboarding-documentation.md` |
| Wiki | `wiki internal knowledge` | `hub/internal-wiki-best-practices.md` |
| Writing style | `style guide concise active` | `hub/writing-style-guide.md` |
| Doc types | `Diataxis tutorial reference` | `hub/diataxis-framework.md` |
| Docs as code | `docs-as-code CI repo` | `research/documentation-as-code.md` |
| Markdown | `markdown syntax GFM` | `entities/markdown-syntax-reference.md` |
| Building seeds | `seedvault creation SAME` | `research/seedvault-creation-guide.md` |

## Key Questions This Seed Answers

1. How do I write a README that people actually read?
2. What documentation types does my project need?
3. How do I structure an ADR, RFC, or runbook?
4. What writing style works best for technical content?
5. Should my docs live in the repo or a wiki?
6. How do I measure documentation quality?
7. How do I deal with outdated documentation?
8. What tools help me write better docs?
9. How do I write for international audiences?
10. How do I create a SAME SeedVault?

## Content Organization

- `hub/` -- Core documentation type overviews, start here for any topic
- `research/` -- Deep dives with implementation details (grows with each session)
- `entities/` -- Quick reference docs for Markdown, templates, checklists
- `guides/` -- Step-by-step how-tos for common documentation tasks
- `decisions/` -- Documentation tooling decision templates and log
- `sessions/` -- Session handoffs for continuity
- `_archive/` -- Safely archived originals from merge process
