---
title: "What Metrics Should I Track for Project Health?"
tags: [metrics, health, analytics, sustainability, growth, downloads, stars, contributor-count]
content_type: research
domain: engineering
---

# What Metrics Should I Track for Project Health?

## The Question

Stars feel good but do not tell you much. What metrics actually indicate whether your project is healthy and growing?

## Metrics That Matter

### Adoption Metrics

| Metric | What It Tells You | Where to Find It |
|--------|-------------------|-------------------|
| **Downloads/installs** | Actual usage | npm stats, PyPI stats, brew analytics |
| **Clones** | People trying your project | GitHub Insights > Traffic |
| **Unique visitors** | Reach and discovery | GitHub Insights > Traffic |
| **Forks** | Potential contributors | GitHub repo page |
| **Dependents** | Projects depending on yours | GitHub Insights > Dependency graph |

### Community Health Metrics

| Metric | What It Tells You | Healthy Range |
|--------|-------------------|---------------|
| **Issue response time** | How fast you respond | Under 48 hours |
| **PR merge time** | How fast contributions land | Under 2 weeks |
| **Open issue count** | Maintenance load | Growing slower than closed |
| **Contributor count** | Community breadth | Growing over time |
| **Returning contributors** | Community depth | At least 20% return |

### Engagement Metrics

| Metric | What It Tells You |
|--------|-------------------|
| **Stars** | Interest/awareness (not usage) |
| **Star velocity** | Growth rate |
| **Discussion activity** | Community engagement |
| **Issue quality** | User sophistication |

## What Stars Actually Measure

Stars measure awareness, not adoption. A project with 50 stars and 1,000 weekly downloads is healthier than one with 5,000 stars and 10 downloads. Do not optimize for stars.

That said, stars do matter for:
- Social proof (visitors check star count)
- Search ranking on GitHub
- Awesome list eligibility (some require minimum stars)
- Your own motivation (they feel good, and that is okay)

## Tools for Tracking

| Tool | What It Tracks | Cost |
|------|---------------|------|
| **GitHub Insights** | Traffic, clones, referrers | Free |
| **Star History** (star-history.com) | Star growth over time | Free |
| **npm stat** (npm-stat.com) | npm download trends | Free |
| **Repobeats** | Activity graphs for README | Free |
| **CHAOSS / GrimoireLab** | Comprehensive community metrics | Free (OSS) |
| **GitHub Actions** | CI pass rate, build times | Free for public repos |

## Health Check Routine

Monthly, check:

1. **Downloads trend** — Flat, growing, or declining?
2. **Open issues** — Manageable or overwhelming?
3. **PR backlog** — Are contributions sitting unreviewed?
4. **Response times** — Are you responding within your stated SLA?
5. **Contributor diversity** — Bus factor still 1?

## Red Flags

- Downloads declining over 3+ months
- Issue response time exceeding 1 week consistently
- No new contributors in 6+ months
- PR backlog growing faster than you can review
- You are the only person who has committed in 3+ months

## Green Flags

- Steady or growing downloads
- Issues from diverse users (not just the same 3 people)
- External contributors submitting quality PRs
- Users answering each other's questions in discussions
- Projects depending on yours (visible in dependency graph)

## Related

- `hub/sustainability.md` — Sustainability overview
- `research/sustainability/preventing-burnout.md` — Burnout prevention
- `entities/github.md` — GitHub analytics features
