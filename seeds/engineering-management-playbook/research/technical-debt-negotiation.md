---
title: "Technical Debt Negotiation with Product"
tags: [technical-debt, product, negotiation, prioritization, refactoring, sustainability, tradeoffs]
content_type: research
domain: engineering-management
---

# Technical Debt Negotiation with Product

## The Core Tension

Product wants features. Engineering wants to fix things. Neither is wrong. The failure is when this becomes adversarial instead of collaborative. Your job as EM: translate technical debt into business language and negotiate sustainable investment.

## Framing Debt in Business Terms

Product managers don't care about "messy code" or "we should refactor the auth module." They care about business outcomes.

**Don't say**: "We need two sprints to refactor the payment service."

**Say**: "The payment service has 47 manual incidents per quarter, each taking 2 hours to resolve. That's 94 engineering hours -- equivalent to one engineer's output for 6 weeks per year. A 2-sprint investment reduces that by 80%, freeing a half-engineer of capacity for feature work."

### The Translation Table

| Technical concept | Business translation |
|-------------------|---------------------|
| Tech debt | Delivery speed tax |
| Refactoring | Capacity investment |
| Test coverage | Deployment confidence (fewer rollbacks) |
| Flaky tests | Developer time drain (X hours/week wasted) |
| Legacy system | Change cost multiplier (features take 3x longer here) |
| Monitoring gaps | Incident severity amplifier (we learn about problems from users, not dashboards) |

## The 20% Rule (and Why It Usually Fails)

"Reserve 20% of capacity for tech debt" sounds reasonable. In practice:
- It gets raided first when deadlines approach
- It creates a false binary (feature sprint vs debt sprint)
- 20% is too little for serious debt, too much for well-maintained systems

**Better approach: embed debt reduction in feature work.**

When planning a feature that touches a debt-heavy area, scope the work to include cleanup. "This feature will take 8 points, of which 3 are debt reduction that makes the next feature in this area cheaper." Product gets their feature. Engineering improves the system. No negotiation needed.

## The Debt Register

Maintain a simple debt register that makes the invisible visible:

```
| Debt Item | Business Impact | Fix Effort | Risk if Ignored |
|-----------|----------------|------------|-----------------|
| Auth module coupling | New auth features take 3x expected | 2 sprints | Blocks SSO integration |
| No staging environment | Bugs found in prod only | 1 sprint | Customer-facing incidents |
| Manual deploy process | 4 hours per deploy, error-prone | 1 sprint | Outage risk on every deploy |
```

Review this with your product partner quarterly. Let them help prioritize. When they see the business impact, most PMs become allies, not adversaries.

## When NOT to Use This Framework

- **Don't negotiate if you don't have data**: "It feels messy" isn't a business case. Measure the impact first.
- **Don't hoard debt work as engineering-only**: If product doesn't understand what you're doing and why, they'll rightfully question invisible work.
- **Don't frame every quality improvement as debt**: Sometimes engineers want to refactor because it's intellectually satisfying, not because it has business impact. That's legitimate, but call it what it is.

## In Reality

Some debt you live with forever. Not everything needs to be clean. The payment system that processes millions of dollars? Keep it pristine. The internal admin tool used by 3 people? It can be ugly.

The teams that handle debt well don't have "tech debt sprints." They treat code health as a continuous practice -- like brushing your teeth, not like dental surgery.

## See Also

- `hub/technical-decision-making.md` -- Deciding what debt to address
- `hub/engineering-metrics.md` -- Measuring debt impact
- `hub/managing-up.md` -- Making the case to leadership
- `hub/sprint-retrospectives.md` -- Where debt pain surfaces
- `research/build-vs-buy.md` -- When replacing is better than fixing
