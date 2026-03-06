---
title: "REST vs GraphQL — Decision Template"
tags: [decision, rest, graphql, architecture, template]
content_type: decision
domain: api-design
---

# REST vs GraphQL — Decision Template

Use this template when deciding between REST and GraphQL for your API.

## Context

[Describe your API: what it does, who consumes it, what data it serves.]

## Decision Criteria

Score each factor 1-5 for your situation:

| Factor | REST | GraphQL | Your Score (REST) | Your Score (GraphQL) |
|--------|------|---------|-------------------|---------------------|
| Client diversity (multiple frontends) | 2 | 5 | __ | __ |
| Data relationship complexity | 2 | 5 | __ | __ |
| Need for HTTP caching | 5 | 2 | __ | __ |
| Team GraphQL experience | N/A | N/A | __ | __ |
| File upload requirements | 5 | 2 | __ | __ |
| Public API (external devs) | 5 | 3 | __ | __ |
| Rapid frontend iteration | 2 | 5 | __ | __ |
| Bandwidth constraints | 2 | 5 | __ | __ |
| Simple CRUD operations | 5 | 3 | __ | __ |
| Real-time requirements | 3 | 4 | __ | __ |

## Questions to Answer

1. How many different clients consume this API? (1 = REST, 3+ = consider GraphQL)
2. Do clients frequently over-fetch or under-fetch? (Yes = GraphQL advantage)
3. Does the team have GraphQL experience? (No = factor in learning curve)
4. Are there file upload/download requirements? (Yes = REST advantage)
5. Is this a public API for external developers? (Yes = REST is more accessible)
6. How complex are the data relationships? (Deep nesting = GraphQL advantage)

## Decision

**Chosen approach:** [REST / GraphQL / Hybrid]

**Rationale:**
[Why this choice is right for your specific situation. Reference the scores above.]

**Tradeoffs accepted:**
[What you're giving up with this choice.]

## Hybrid Approach (If Applicable)

If choosing both:
- REST for: [which endpoints/use cases]
- GraphQL for: [which endpoints/use cases]
- Shared: [auth, rate limiting, monitoring]

## See Also

- `entities/graphql-vs-rest.md` -- Detailed comparison
- `hub/rest-fundamentals.md` -- REST design principles
- `hub/graphql-fundamentals.md` -- GraphQL design principles
