---
title: "Measuring Documentation Quality"
tags: [metrics, quality, time-to-success, support-tickets, documentation, measurement]
content_type: research
domain: technical-writing
---

# Measuring Documentation Quality

## The Problem

"Our docs are good" means nothing without measurement. "Our docs reduced support tickets by 30% and cut onboarding time from 5 days to 2" means everything.

## Metrics That Matter

### Time to First Success

How long does it take a new user to go from "I found your docs" to "I got my first result"? This is the single most important metric for developer documentation.

**How to measure:**
- Time from first page visit to first successful API call
- Time from clone to running dev environment
- Survey new hires: "How long did setup take?"

**Target:** Under 15 minutes for API quickstart. Under 1 hour for full dev environment setup.

### Support Ticket Deflection

Documentation should answer questions before they become support tickets.

**How to measure:**
- Track support tickets by category
- Compare ticket volume before and after doc improvements
- Tag tickets as "answered in docs" vs "not documented"

**What it tells you:** If tickets drop after you document a topic, the docs are working. If tickets persist for a documented topic, the docs aren't findable or clear enough.

### Search Success Rate

**How to measure:**
- Track internal doc search queries
- Measure zero-result queries (documentation gaps)
- Track click-through rate on search results
- Monitor "search then leave" (person searched, didn't find, left)

### Page-Level Signals

- **Time on page.** Very short = didn't find what they needed. Very long = confusing content.
- **Bounce rate.** High bounce on landing pages = content doesn't match expectations.
- **Feedback widgets.** "Was this helpful? Yes/No" with optional text feedback. Simple and effective.

### Developer Satisfaction

Periodic surveys (quarterly, not more often):
- "How easy is it to find information in our docs?" (1-5)
- "When you last used our docs, did they answer your question?" (Yes/No)
- "What's missing from our docs?"

## Metrics That Don't Matter

- **Page count.** 200 docs is not better than 50 if 150 are outdated.
- **Word count.** More words often means less clarity.
- **Coverage percentage.** 100% coverage with bad quality is worse than 60% coverage with great quality.
- **Update frequency.** Updating docs for the sake of updating them adds noise.

## Building a Measurement Practice

1. **Start with one metric.** Pick time-to-first-success or support ticket deflection.
2. **Establish a baseline.** Measure the current state before making changes.
3. **Make one improvement.** Rewrite the quickstart, add missing error docs, fix navigation.
4. **Measure again.** Did the metric improve?
5. **Repeat.** Add more metrics as the practice matures.

## The Feedback Loop

```
Measure → Identify worst-performing area → Improve it → Measure again
```

Don't try to improve everything at once. Find the highest-impact gap and fix it. Then find the next one.

## See Also

- `research/search-optimized-documentation.md` -- Findability as a quality dimension
- `research/documentation-debt.md` -- When quality degrades
- `entities/documentation-maturity-model.md` -- Maturity levels include measurement
- `hub/onboarding-documentation.md` -- Time-to-first-success for onboarding
