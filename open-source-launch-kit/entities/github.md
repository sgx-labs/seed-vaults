---
title: "GitHub — Platform Features for OSS"
tags: [github, entity, platform, actions, releases, discussions]
content_type: entity
domain: engineering
---

# GitHub — Platform Features for OSS

## What It Is
The dominant platform for hosting open source projects. Free for public repos with generous CI/CD minutes, release hosting, and community features.

## Key Features for OSS Projects

| Feature | Purpose | Setup |
|---------|---------|-------|
| Actions | CI/CD automation | `.github/workflows/*.yml` |
| Releases | Binary distribution, changelogs | Tags trigger release workflow |
| Discussions | Q&A, announcements, community | Settings > Features > Discussions |
| Projects | Kanban boards, roadmap tracking | Repo > Projects tab |
| Issues | Bug reports, feature requests | Enabled by default |
| Pages | Documentation hosting | Settings > Pages |
| Security Advisories | Vulnerability disclosure | Security tab > Advisories |
| Sponsors | Funding from users/companies | Profile > Sponsors tab |

## Repository Settings to Configure

- **Description** — One-line summary (appears in search results)
- **Topics** — Tags for discoverability (up to 20)
- **Social preview** — 1280x640px image for link previews
- **Default branch protection** — Require PR reviews, status checks
- **Issue templates** — Bug report, feature request, question
- **PR template** — Checklist for contributors

## GitHub Actions Free Tier (Public Repos)

- Unlimited minutes for public repositories
- Hosted runners: Ubuntu, macOS, Windows
- Concurrent jobs: 20 (free tier)
- Artifact storage: 500 MB
- Cache storage: 10 GB

## Issue Labels Worth Creating

- `good first issue` — Shown in GitHub's contributor search
- `help wanted` — Also surfaced in GitHub's contributor search
- `bug`, `enhancement`, `documentation` — Standard triage
- `breaking-change` — Flag for release notes
- `wontfix` — Clear communication of scope

## Useful Integrations

- **Dependabot** — Automated dependency updates
- **CodeQL** — Free security scanning for public repos
- **GitHub Apps** — Stale bot, welcome bot, CLA assistant

## Last Updated
February 2026
