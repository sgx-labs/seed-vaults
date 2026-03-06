---
title: "API Design Patterns — Seed Spec"
tags: [seed-spec, planning, structure, quality-checklist, api-design]
content_type: hub
domain: api-design
---

# API Design Patterns — Seed Spec

## Metadata
- **Name**: api-design-patterns
- **Type**: Knowledge
- **Audience**: Backend developers building or maintaining APIs
- **Note count target**: 50-55
- **Agent build time**: 4-6 hours

## Purpose

Comprehensive reference for backend developers who need to make API design decisions. Covers REST, GraphQL, gRPC, authentication, authorization, rate limiting, versioning, error handling, caching, webhooks, and production patterns. Every note should help a developer make a better decision or avoid a common mistake.

## Hub Categories

1. hub/rest-fundamentals.md — Resource naming, HTTP methods, status codes, REST constraints
2. hub/authentication.md — API keys, OAuth2, JWT, session tokens, when to use each
3. hub/authorization.md — RBAC, ABAC, scopes, permission models
4. hub/rate-limiting.md — Sliding window, token bucket, fixed window, per-user vs global
5. hub/pagination.md — Cursor vs offset, keyset pagination, tradeoffs
6. hub/versioning.md — URL, header, query param strategies and tradeoffs
7. hub/error-handling.md — RFC 7807 Problem Details, error codes, consistency
8. hub/request-validation.md — Input validation, schemas, sanitization
9. hub/idempotency.md — Idempotency keys, safe vs unsafe methods, retry safety
10. hub/caching.md — ETags, Cache-Control, conditional requests, invalidation
11. hub/cors.md — Cross-origin configuration, preflight, security implications
12. hub/webhooks.md — Webhook design, delivery guarantees, verification
13. hub/graphql-fundamentals.md — Schema design, resolvers, N+1, batching
14. hub/grpc-fundamentals.md — Protobuf, streaming, service definitions, when to use
15. hub/api-gateway.md — Gateway patterns, routing, aggregation, BFF

## Key Questions (agent research targets)

1. How do I choose between OAuth2, JWT, and API keys for my API?
2. What's the best pagination strategy for large datasets?
3. How do I version my API without breaking clients?
4. What error format should I use and how do I keep it consistent?
5. How do I implement rate limiting without a Redis dependency?
6. When should I use GraphQL instead of REST?
7. How do I design webhooks that are reliable and secure?
8. What does a production-ready API security checklist look like?
9. How do I handle file uploads in a REST API?
10. What's the right way to do batch operations?
11. How do I implement circuit breakers for downstream services?
12. What retry strategy should I use for failed requests?
13. How do I monitor API health and performance?
14. What's the best way to handle real-time data (SSE vs WebSocket vs polling)?
15. How do I design a multi-tenant API?

## Entities

1. entities/http-status-codes.md — Complete reference with usage guidance
2. entities/content-type-headers.md — Common content types and when to use them
3. entities/oauth2-grant-types.md — Quick reference for all OAuth2 flows
4. entities/security-headers.md — Common security headers and their purpose
5. entities/rest-maturity-model.md — Richardson Maturity Model levels
6. entities/api-design-tools.md — Postman, Insomnia, Bruno, httpie
7. entities/openapi-quick-reference.md — OpenAPI 3.x spec essentials
8. entities/graphql-vs-rest.md — Side-by-side comparison
9. entities/api-testing-frameworks.md — Tools for testing APIs
10. entities/rate-limit-headers.md — X-RateLimit-* header conventions

## Decisions to Pre-Populate

1. REST vs GraphQL decision template — criteria, tradeoffs, recommendation framework
2. Auth strategy decision template — choosing between OAuth2, JWT, API keys, sessions
3. Database choice for API backend — relational vs document vs key-value for different API patterns

## Water the Seed Protocol

### 1. Scan & Merge
- Agent reads existing CLAUDE.md before suggesting changes
- Proposes merged version — shows diff, waits for approval
- Archives originals to `_archive/` before writing changes
- Never overwrites user work

### 2. Gap Analysis
- After indexing, compare seed coverage against user's actual API
- Search vault for each key question — identify what's covered, what's missing
- Factor in user's tech stack, framework, and deployment target

### 3. Research Recommendations
- Present numbered list of recommended research topics based on gaps
- Offer to spin up parallel research agents for approved topics
- New research gets saved as notes in the user's vault

### 4. SAME Showcase Behaviors
- Use `same search` to find answers before generating from scratch
- Use `same save_decision` when the user makes an API design decision
- Use `same create_handoff` at session end
- Use `same find_similar_notes` to cross-reference related patterns
- Use `same search_across_vaults` when the user has multiple seeds
- Surface these capabilities naturally — don't lecture, demonstrate

## Config Notes

```toml
[vault]
  path = "."
  skip_dirs = ["_Raw", "_archive"]
  handoff_dir = "sessions"
  decision_log = "decisions/log.md"

[memory]
  composite_threshold = 0.65
  max_results = 4
```

## Quality Checklist
- [ ] Every note under 200 lines
- [ ] YAML frontmatter on all notes (title, tags, content_type, domain)
- [ ] bootstrap.md with pinned: true and "Water the Seed" section
- [ ] CLAUDE.md with merge protocol and SAME showcase behaviors
- [ ] config.toml.example (provider-agnostic)
- [ ] No PII, no API keys, no secrets
- [ ] `_archive/` in skip_dirs
- [ ] `same reindex` produces 0 errors
- [ ] `same search` returns relevant results for 5 test queries
- [ ] Key questions are answerable by searching the vault
- [ ] SAME tools are demonstrated naturally in CLAUDE.md examples
