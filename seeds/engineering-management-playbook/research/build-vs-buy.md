---
title: "When to Build vs Buy vs Open Source"
tags: [build-vs-buy, make-or-buy, open-source, vendor, SaaS, decision-framework, tradeoffs]
content_type: research
domain: engineering-management
---

# When to Build vs Buy vs Open Source

## The Default Answer

Buy. Unless you have a specific, defensible reason to build, buy a SaaS product or adopt an open-source solution. Engineers systematically underestimate the cost of building and maintaining custom solutions.

## The Decision Framework

### Build When:
- It's your core differentiator (the thing that makes your product uniquely valuable)
- No existing solution handles your specific constraints (scale, compliance, latency)
- You need deep integration that vendor APIs can't provide
- The domain is so specific that off-the-shelf solutions would require extensive customization anyway
- You have the team capacity to build AND maintain it long-term

### Buy When:
- It's a solved problem (auth, payments, email, monitoring, CI/CD)
- The vendor's expertise exceeds yours (security products, compliance tools)
- Time-to-market matters more than customization
- The cost is reasonable relative to engineering time saved
- You don't want to hire specialists to maintain it

### Open Source When:
- You need flexibility the vendor doesn't offer
- You have engineers who can contribute and maintain
- The project is actively maintained with a healthy community
- You need to avoid vendor lock-in
- Cost is a primary concern and you have ops capacity

## The Hidden Cost of Building

Engineers estimate the build cost. They almost never estimate:
- **Maintenance**: Bug fixes, security patches, version upgrades (2-4x build cost over 3 years)
- **On-call**: Someone has to be paged when it breaks
- **Documentation**: For the team, for new hires, for compliance
- **Feature requests**: Once it exists, people will want more from it
- **Opportunity cost**: The features you didn't build because your team was maintaining infrastructure

A rule of thumb: multiply the estimated build time by 4 to get the true 3-year cost.

## The Hidden Cost of Buying

- **Vendor dependency**: If they shut down, raise prices, or degrade quality, you're stuck
- **Integration tax**: Getting vendor products to work with your stack takes time
- **Data ownership**: Your data lives on someone else's servers
- **Feature pace**: You're dependent on their roadmap, not yours
- **Customization limits**: "Almost what we need" is the most expensive phrase in software

## Real Examples

**Auth (Buy)**: Don't build your own auth. Use Auth0, Clerk, or Firebase Auth. The security implications of getting auth wrong are catastrophic, and you'll never invest enough to match a dedicated auth provider.

**Monitoring (Buy or Open Source)**: Datadog, Grafana Cloud, or self-hosted Prometheus+Grafana. Building your own monitoring is a years-long project that will never be as good.

**Internal Tools (Open Source + Customize)**: Retool, Appsmith, or build lightly. Internal tools are high value but low differentiation -- spend as little engineering time as possible.

**Core Business Logic (Build)**: Your recommendation engine, pricing algorithm, or workflow orchestration that IS your product. Build this in-house.

## When NOT to Use This Framework

- **When the decision is already political**: Sometimes "build vs buy" is really "my team wants to build cool stuff vs the CFO wants to reduce headcount." Name the real conversation.
- **For trivial decisions**: If it takes less than a day to build and has no maintenance burden, just build it.
- **When you're already locked in**: If you're 2 years into a custom solution, the "build" cost is sunk. Evaluate based on forward costs only.

## See Also

- `hub/technical-decision-making.md` -- RFC process for evaluating build vs buy
- `research/technical-debt-negotiation.md` -- Custom builds that become debt
- `hub/engineering-metrics.md` -- Measuring the true cost of custom solutions
- `entities/adr-template.md` -- Document the build/buy/OSS decision
- `hub/managing-up.md` -- Presenting the recommendation to leadership
