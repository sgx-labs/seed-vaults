---
title: "Bootstrap — New Session Quick Start"
tags: [bootstrap, onboarding, session-start, water-the-seed, research-cascade, orientation, api-design]
content_type: hub
domain: api-design
pinned: true
---

# Bootstrap — New Session Quick Start

## Water the Seed

First time? Your agent will handle everything. Just run:

```bash
same init && same reindex
```

Then start a conversation. Your agent reads this file automatically and begins the Research Cascade -- a multi-phase process that scans your project, understands your API, asks smart questions, and fills your vault with personalized knowledge. The more you interact, the smarter it gets.

**What happens next (automatic):**
1. Agent silently scans your project -- routes, middleware, auth, database, API docs
2. Agent presents an insight report showing what it learned about your API
3. Agent asks 3-5 targeted questions about your API design and pain points
4. Agent proposes CLAUDE.md additions (archives originals safely)
5. Agent recommends research topics specific to YOUR API
6. You pick which topics matter -- agents research in parallel, save results to your vault
7. Each research result suggests new topics -- the vault grows exponentially

After the first session, your vault has generic API design knowledge PLUS project-specific intelligence that no one else has.

## Returning Sessions

Already set up? Here is what your agent does automatically on every session:

- **Loads context** -- SAME surfaces your pinned notes, recent handoffs, and decisions
- **Remembers where you left off** -- session handoffs capture what was done and what is pending
- **Searches before generating** -- your vault has 53+ expert notes, growing with every session
- **Cross-references** -- finds related patterns you might not know about
- **Compounds knowledge** -- every useful answer gets saved for next time

## Where To Find Things

| Need | Search for | Or read directly |
|------|-----------|-----------------|
| REST design | `REST resources HTTP methods` | `hub/rest-fundamentals.md` |
| Authentication | `auth OAuth JWT API key` | `hub/authentication.md` |
| Error handling | `error format RFC 7807` | `hub/error-handling.md` |
| Pagination | `pagination cursor offset` | `hub/pagination.md` |
| Rate limiting | `rate limit throttle` | `hub/rate-limiting.md` |
| Versioning | `version URL header` | `hub/versioning.md` |
| Caching | `cache ETag conditional` | `hub/caching.md` |
| Webhooks | `webhook event callback` | `hub/webhooks.md` |
| GraphQL | `GraphQL schema resolver` | `hub/graphql-fundamentals.md` |
| gRPC | `gRPC protobuf streaming` | `hub/grpc-fundamentals.md` |
| Security | `OWASP security checklist` | `research/api-security-checklist.md` |
| OpenAPI | `OpenAPI Swagger spec` | `research/openapi-best-practices.md` |
| Status codes | `HTTP status code` | `entities/http-status-codes.md` |
| How-to guides | `guide tutorial step-by-step` | `guides/` |

## Key Questions This Seed Answers

1. How do I design clean, consistent REST endpoints?
2. What authentication approach should I use for my API?
3. How do I paginate large result sets efficiently?
4. What's the best error format for API responses?
5. How do I version my API without breaking existing clients?
6. When should I use GraphQL instead of REST?
7. How do I implement rate limiting that scales?
8. What does a production API security checklist look like?
9. How do I design reliable webhooks?
10. What caching strategy fits my API?

## Content Organization

- `hub/` -- Core pattern overviews, start here for any topic
- `research/` -- Deep dives with implementation details (grows with each session)
- `entities/` -- Quick reference docs for status codes, headers, tools
- `guides/` -- Step-by-step how-tos for common API tasks
- `decisions/` -- Architectural decision templates and log
- `sessions/` -- Session handoffs for continuity
- `_archive/` -- Safely archived originals from merge process
