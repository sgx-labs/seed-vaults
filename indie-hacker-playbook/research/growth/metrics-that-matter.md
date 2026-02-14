---
title: "Metrics That Matter — What to Track, What to Ignore"
tags: [growth, metrics, analytics, mrr, churn]
content_type: research
domain: business
---

# Metrics That Matter — What to Track, What to Ignore

## The Only Metrics That Matter Early On

At the early stage, most metrics are noise. Focus on these five:

### 1. Monthly Recurring Revenue (MRR)
What it is: Total monthly subscription revenue from all paying customers.

MRR = (Number of customers) x (Average monthly price)

This is THE number. Everything else is secondary.

### 2. Churn Rate
What it is: Percentage of customers who cancel each month.

Churn = (Customers lost in month) / (Customers at start of month) x 100

| Churn rate | Interpretation |
|-----------|---------------|
| Under 3% | Excellent — strong product-market fit |
| 3-5% | Good — normal for early-stage |
| 5-10% | Concerning — investigate why people leave |
| Above 10% | Critical — fix retention before growing |

### 3. Conversion Rate (Trial to Paid)
What it is: Percentage of free trial users who become paying customers.

| Rate | Interpretation |
|------|---------------|
| Under 2% | Pricing, onboarding, or product issue |
| 2-5% | Normal range |
| 5-10% | Good — product delivers value quickly |
| Above 10% | Consider raising prices |

### 4. Customer Acquisition Cost (CAC)
What it is: How much you spend to acquire one customer.

CAC = (Total marketing spend) / (New customers acquired)

For indie hackers with $0 marketing budget, your CAC is your time. Track hours spent on marketing per customer acquired.

### 5. Revenue per Customer (ARPU)
What it is: Average revenue per user per month.

ARPU = MRR / Number of customers

If ARPU is low, either raise prices or move upmarket.

## Metrics to Ignore (For Now)

| Metric | Why to ignore |
|--------|--------------|
| Total signups | Vanity metric — paid customers matter |
| Page views | Traffic without conversion is meaningless |
| Social media followers | Followers do not pay bills |
| Feature usage analytics | Too early to optimize — ship and iterate |
| Net Promoter Score | You do not have enough users for statistical significance |
| Lifetime Value (LTV) | Need 6+ months of data to calculate meaningfully |

## When to Start Tracking What

| Stage | Track these |
|-------|------------|
| Pre-launch | Waitlist signups, landing page conversion |
| 0-10 customers | MRR, trial-to-paid conversion |
| 10-100 customers | MRR, churn, ARPU, top acquisition channel |
| 100+ customers | All of the above + LTV, CAC, cohort analysis |

## Setting Up Analytics Without Overcomplicating

### The Minimal Stack
1. **Plausible or Vercel Analytics** — website traffic (privacy-friendly, simple)
2. **Stripe Dashboard** — MRR, churn, revenue charts
3. **A spreadsheet** — track MRR, new customers, churned customers monthly

That is it. Do not set up Mixpanel, Amplitude, or a custom analytics dashboard until you have 100+ customers.

### The Monthly Metrics Review

Every month, spend 30 minutes reviewing:
1. MRR this month vs. last month (growing?)
2. How many new customers (from where?)
3. How many churned (why?)
4. Conversion rate (improving?)
5. One insight to act on this month

## The Spreadsheet Template

```
Month | MRR | New Customers | Churned | Total Customers | Churn % | Top Channel
Jan   | $0  | 0             | 0       | 0               | 0%      | -
Feb   | $87 | 3             | 0       | 3               | 0%      | Direct outreach
Mar   | $203| 5             | 1       | 7               | 14%     | Product Hunt
...
```

Keep it simple. Update it on the 1st of every month. Look at trends, not individual data points.
