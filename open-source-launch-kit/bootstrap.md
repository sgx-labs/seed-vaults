---
title: "Bootstrap — Open Source Launch Kit"
tags: [bootstrap, onboarding, session-start, critical]
content_type: hub
domain: engineering
pinned: true
---

# Bootstrap — Open Source Launch Kit

## 1. What This Is

A complete knowledge base for launching, growing, and maintaining an open source project. From writing your README to handling your first contributor PR to preventing burnout at year two. Search for what you need, get battle-tested advice instantly.

## 2. Where To Find Things

| Need | Search for | Or read directly |
|------|-----------|-----------------|
| Launch checklist | `pre-launch checklist release` | `hub/launch.md` |
| README help | `readme converts visitors users` | `research/docs/readme-that-converts.md` |
| Show HN strategy | `show hn hacker news launch` | `research/launch/show-hn-playbook.md` |
| First contributors | `contributor PR handling` | `research/community/first-contributor-pr.md` |
| License choice | `license MIT Apache choose` | `research/launch/choosing-a-license.md` |
| GitHub Actions CI | `github actions ci release` | `research/releases/github-actions-ci.md` |
| npm publishing | `npm publish provenance` | `entities/npm.md` |
| Community building | `discord community grow` | `hub/community.md` |
| Funding options | `sponsors funding grants` | `hub/sustainability.md` |
| Changelog format | `changelog keep format` | `research/releases/writing-changelogs.md` |
| Burnout prevention | `burnout maintainer balance` | `research/sustainability/preventing-burnout.md` |
| Version strategy | `semantic versioning bump` | `research/releases/semantic-versioning.md` |

## 3. How To Use This Seed

```bash
# Search for anything
same search "how to write a good README"

# Via MCP
mcp__same__search_notes(query="handling breaking changes", top_k=5)

# Find related notes
mcp__same__find_similar_notes(path="research/launch/show-hn-playbook.md", top_k=3)
```

## 4. Project Phase Tracker

Update this as your project progresses:

- [ ] **Pre-launch** — Code ready, README written, license chosen
- [ ] **Soft launch** — Shared with friends, collected feedback
- [ ] **Public launch** — Show HN / Product Hunt / directories
- [ ] **Growth** — First 100 stars, first external contributors
- [ ] **Sustaining** — Regular releases, community management, funding

## 5. Key Concepts

- **README as landing page** — Your README is your marketing. It has 30 seconds to convince a visitor to try your project.
- **Semantic versioning** — MAJOR.MINOR.PATCH. Users trust version numbers to tell them what changed.
- **Keep a Changelog** — Structured format: Added, Changed, Deprecated, Removed, Fixed, Security.
- **CONTRIBUTING.md** — Lower the barrier for first-time contributors. Welcome them explicitly.
- **Security policy** — SECURITY.md tells people how to report vulnerabilities privately.
- **Bus factor** — How many people need to disappear before the project dies? Aim for more than one.

## 6. Content Organization

- `hub/` — Category overviews, start here for any topic
- `research/` — Detailed guides, one question per file
- `entities/` — Living docs about platforms that change over time
- `decisions/` — Architectural decisions for your project
- `templates/` — Fill-in templates for common files
