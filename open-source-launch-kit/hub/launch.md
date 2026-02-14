---
title: "Launch — Pre-Launch Checklist and First Release"
tags: [launch, checklist, first-release, go-live]
content_type: hub
domain: engineering
---

# Launch — Pre-Launch Checklist and First Release

## Overview

Launching an open source project is part engineering, part marketing. You need working code AND a story that makes people care. This hub covers everything from "is my repo ready?" to "how do I get my first users?"

## Pre-Launch Checklist

### Repository Essentials

- [ ] README.md — clear value proposition, install instructions, usage examples
- [ ] LICENSE — chosen and added (see `research/launch/choosing-a-license.md`)
- [ ] CONTRIBUTING.md — how to contribute (see `templates/contributing-template.md`)
- [ ] CHANGELOG.md — initial entry (see `templates/changelog-template.md`)
- [ ] SECURITY.md — vulnerability reporting process
- [ ] CODE_OF_CONDUCT.md — Contributor Covenant or similar
- [ ] .gitignore — appropriate for your language/framework
- [ ] CI/CD — tests run on every PR (see `research/releases/github-actions-ci.md`)

### Code Quality

- [ ] Tests pass and cover critical paths
- [ ] No hardcoded secrets, API keys, or personal paths
- [ ] Dependencies are pinned or locked
- [ ] Build instructions actually work on a clean machine
- [ ] Error messages are helpful, not cryptic

### Project Metadata

- [ ] Repository description filled in on GitHub
- [ ] Topics/tags added (helps discoverability)
- [ ] Social preview image set (1280x640px)
- [ ] Website URL field populated (even if just the README)
- [ ] GitHub Discussions enabled (for Q&A)

## Launch Sequence

1. **Soft launch** — Share with 10-20 trusted developers. Collect feedback.
2. **Fix friction** — Address every install/setup issue they hit.
3. **Public launch** — Post to Show HN, directories, communities.
4. **Engage** — Respond to every comment within the first 48 hours.
5. **Sustain** — Keep shipping. One launch is not enough.

## Research Notes

- `research/launch/minimum-viable-oss-project.md` — What you actually need before going public
- `research/launch/choosing-a-license.md` — MIT vs Apache vs GPL decision guide
- `research/launch/show-hn-playbook.md` — How to launch on Hacker News
- `research/launch/product-hunt-playbook.md` — Product Hunt launch strategy
- `research/launch/first-100-stars.md` — Getting initial traction

## Related

- `hub/docs.md` — Documentation strategy
- `hub/marketing.md` — Distribution channels beyond launch day
