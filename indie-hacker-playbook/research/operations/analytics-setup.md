---
title: "Analytics Setup — Simple Analytics Without the Complexity"
tags: [operations, analytics, tracking, plausible, sentry]
content_type: research
domain: business
---

# Analytics Setup — Simple Analytics Without the Complexity

## The Problem

Most founders set up too much analytics too early. They install Mixpanel, Google Analytics, Amplitude, Hotjar, and three other tools. Then they spend hours looking at dashboards instead of building and selling.

## The Minimal Viable Analytics Stack

You need exactly three things:

### 1. Website Analytics (traffic)
**Pick one:**
- **Plausible** ($9/mo) — Privacy-friendly, simple, no cookie banner needed
- **Vercel Analytics** (included with Vercel) — If you are already on Vercel
- **PostHog** (free tier) — More features, open source, can grow with you
- **Umami** (free, self-hosted) — Open source, own your data

**What to track:** Page views, traffic sources, top pages. That is it.

**Do NOT use Google Analytics** unless you specifically need it. It is complex, requires cookie consent, and gives you more data than you need at this stage.

### 2. Payment Analytics (revenue)
**Use your payment processor's dashboard:**
- Stripe Dashboard — MRR, churn, revenue per customer, all built in
- Lemon Squeezy Dashboard — Same metrics
- ChartMogul or Baremetrics (later) — When you want deeper revenue analytics

### 3. Error Tracking
**Pick one:**
- **Sentry** (free tier: 5K events/mo) — Industry standard
- **LogRocket** (free tier) — Session replay + error tracking
- **Your hosting provider's logs** — Vercel, Railway, and Fly.io all have basic logging

## What to Track at Each Stage

### Pre-Launch
- Landing page visits
- Waitlist signups
- Signup conversion rate (signups / visits)

### Launch (0-100 users)
- Where traffic comes from (referrer)
- Signup count
- Trial-to-paid conversion
- MRR (yes, just look at Stripe)

### Growth (100+ users)
- All of the above
- Churn rate
- Feature usage (which features do people actually use?)
- Customer acquisition channel effectiveness
- Add PostHog or Mixpanel for product analytics

## Setup Checklist

1. [ ] Install ONE website analytics tool (Plausible or Vercel Analytics)
2. [ ] Verify Stripe/LS dashboard shows revenue metrics
3. [ ] Install Sentry for error tracking
4. [ ] Create a simple spreadsheet for monthly metrics tracking
5. [ ] Schedule a monthly 30-minute metrics review

That is it. Total setup time: 30 minutes. Monthly maintenance: 30 minutes.

## Analytics Anti-Patterns

| Anti-pattern | Why it is bad | What to do instead |
|-------------|--------------|-------------------|
| Checking metrics hourly | Anxiety-inducing, no actionable data | Check daily or weekly |
| Tracking 50+ events | Noise overwhelms signal | Track 3-5 key events |
| Building custom dashboards | Time sink | Use built-in dashboards |
| A/B testing with 10 users | Not statistically significant | Wait until 100+ monthly conversions |
| Optimizing before product-market fit | Premature optimization | Ship features, talk to users |

## When to Level Up

Add more sophisticated analytics when:
- You have 100+ active users and need to understand feature usage
- You have 50+ monthly trials and want to optimize onboarding
- You have enough traffic for meaningful A/B tests (1,000+ visits/month)
- You can justify the time investment (30+ minutes/week on analytics)

Until then, keep it simple: traffic source, signups, MRR, churn. Everything else is noise.
