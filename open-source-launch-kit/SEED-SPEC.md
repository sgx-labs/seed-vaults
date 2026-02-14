---
title: "Open Source Launch Kit Seed Specification"
content_type: hub
tags: [seed-spec, open-source, launch-kit, project-structure, metadata]
---

# Open Source Launch Kit Seed

## Metadata
- **Name**: open-source-launch-kit
- **Type**: Hybrid (starter content + templates to fill)
- **Audience**: Developers launching or maintaining open source projects
- **Note count target**: 40-60
- **Agent build time**: 2-3 hours

## Purpose
Everything you need to launch, grow, and maintain an open source project. From README writing to community building to release management. Your agent knows the playbook so you can focus on the code.

## Hub Categories

1. hub/launch.md — Pre-launch checklist, first release, initial marketing
2. hub/community.md — Community building, contributors, governance
3. hub/docs.md — Documentation strategy, README, guides, API docs
4. hub/releases.md — Versioning, changelogs, CI/CD, distribution
5. hub/marketing.md — Distribution channels, directories, content marketing
6. hub/sustainability.md — Funding, sponsorships, burnout prevention

## Key Questions

1. How do I write a README that converts visitors to users?
2. What's the minimum viable open source project structure?
3. How do I choose a license?
4. What CI/CD setup should I use for releases?
5. How do I write a good CONTRIBUTING.md?
6. What directories and platforms should I list my project on?
7. How do I do a Product Hunt / Show HN / DevHunt launch?
8. How do I manage issues and PRs from contributors?
9. How do I write a changelog that people actually read?
10. How do I set up GitHub Actions for testing and releases?
11. What's semantic versioning and when do I bump what?
12. How do I build a Discord/community around my project?
13. How do I get my first 100 GitHub stars?
14. How do I handle breaking changes without losing users?
15. How do I prevent burnout as a solo maintainer?
16. What funding options exist (sponsors, grants, open collective)?
17. How do I write good release notes?
18. How do I set up npm/homebrew/binary distribution?
19. What metrics should I track for project health?
20. How do I handle security vulnerabilities responsibly?

## Entities

1. entities/github.md — GitHub features for OSS (Actions, Releases, Discussions, Projects)
2. entities/npm.md — npm publishing, scoped packages, provenance
3. entities/homebrew.md — Tap setup, formula writing, updates
4. entities/product-hunt.md — Launch strategy, timing, preparation
5. entities/discord.md — Community setup, bots, moderation

## Decisions to Pre-Populate

1. AD-001: MIT License unless strong reason otherwise — maximum adoption, minimum friction
2. AD-002: Semantic versioning from day one — users need to trust version numbers
3. AD-003: GitHub Actions for CI — free for public repos, ecosystem is massive
4. AD-004: CHANGELOG.md follows Keep a Changelog format — structured, parseable, readable

## CLAUDE.md Governance

- This seed is your OSS project's institutional memory
- When you make a project decision, log it in decisions/
- Keep entity files updated as platforms change their rules
- Research notes are reference — adapt patterns to your specific project
- Templates in templates/ are starting points, customize for your project

## Config Notes

```toml
[vault]
  path = "."
  skip_dirs = ["_Raw", "templates"]
  handoff_dir = "sessions"
  decision_log = "decisions/log.md"

[memory]
  max_token_budget = 1200
  max_results = 4
  composite_threshold = 0.65
```

## Why This Seed

- **Meta-marketing** — a seed about launching OSS projects, built with an OSS tool
- **Our adjacent audience** — OSS maintainers are power users of dev tools
- **Demonstrates SAME for non-code knowledge** — project management, community, marketing
- **Free forever** — pure marketing vehicle for SAME
- **Self-referential** — SAME's own launch used these exact patterns
