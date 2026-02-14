---
title: "What Funding Options Exist for Open Source Projects?"
tags: [funding, sponsors, grants, monetization, sustainability, github-sponsors, open-collective, open-core]
content_type: research
domain: engineering
---

# What Funding Options Exist for Open Source Projects?

## The Question

You are spending real time on your project. Can you get paid for it? What are the realistic options?

## Funding Options (By Accessibility)

### Tier 1: Easy to Set Up, Small Amounts

**GitHub Sponsors**
- Individual or organization sponsorship tiers
- Users sponsor directly from your GitHub profile
- GitHub takes 0% fees (they subsidize this)
- Set up: Profile > Sponsors tab > Enable
- Realistic income: $0-500/month for most projects
- Best for: Any project with grateful users

**Open Collective**
- Fiscal sponsorship through Open Source Collective (501(c)(6))
- Transparent finances — anyone can see income and expenses
- 10% platform fee
- Handles taxes, accounting, and legal entity
- Best for: Projects that want transparent community funding

**Buy Me a Coffee / Ko-fi**
- One-time tips from users
- Lower barrier than recurring sponsorship
- Not specifically for OSS but works fine
- Realistic income: Coffee money, not a salary

### Tier 2: Grants and Programs

**GitHub Secure Open Source Fund**
- $10,000 per project for security improvements
- 3-week program with workshops and milestones
- Rolling applications
- Focus: Security improvements to critical OSS

**Sovereign Tech Fund** (EU-based)
- Funds critical open digital infrastructure
- Grants from EUR 50K to 300K+
- Focus: Infrastructure used by European public interest

**NLNet Foundation**
- Grants for internet-related open source projects
- Typically EUR 5K-50K
- Regular open calls for proposals
- Focus: Privacy, security, internet standards

**Linux Foundation / CNCF Funding**
- For projects that join these foundations
- Covers infrastructure, events, and sometimes developer time
- Requires significant project maturity

### Tier 3: Business Models

**Open Core**
- Core project is open source, premium features are paid
- Examples: GitLab (CE vs EE), Grafana, Elastic
- Works when: Enterprise users need features individuals do not
- Risk: Community resentment if too much goes behind the paywall

**Support and Services**
- Paid support contracts for companies using your project
- Consulting, custom development, training
- Works when: Companies depend on your project in production
- Risk: Can become a consulting business, not an OSS project

**Dual Licensing**
- Open source under copyleft (AGPL), commercial license available
- Companies that cannot use AGPL pay for a permissive license
- Works when: Your project is used in proprietary software
- Example: MySQL, MongoDB (historically)

**SaaS / Hosted Version**
- Host and manage your tool as a service
- Works when: Self-hosting is complex and companies want convenience
- Risk: Cloud providers may offer competing hosted versions

## Realistic Expectations

- Most OSS projects earn $0
- GitHub Sponsors averages $10-50/month for small projects
- Sustainable full-time income requires 10K+ users or enterprise adoption
- Grants are competitive but realistic for quality projects
- Do not quit your job until you have 6+ months of stable funding

## Getting Started

1. Enable GitHub Sponsors (takes 5 minutes)
2. Add a FUNDING.yml to your repo
3. Mention sponsorship in your README (tastefully)
4. Apply to relevant grants when you meet their criteria
5. Consider open core only after you have significant adoption

### FUNDING.yml Example

```yaml
# .github/FUNDING.yml
github: [your-github-username]
open_collective: your-project-name
custom: ["https://your-project.dev/sponsor"]
```

## Related

- `hub/sustainability.md` — Sustainability overview
- `research/sustainability/preventing-burnout.md` — Burnout prevention
- `research/sustainability/project-health-metrics.md` — Measuring project health
