---
title: "API Design Patterns — Governance"
content_type: hub
tags: [governance, api-design, seed, rules, research-cascade]
pinned: true
domain: api-design
---

# API Design Patterns — Governance

## Purpose

Knowledge seed for designing, building, and maintaining production APIs. Search before generating -- the answer is likely already here.

## Water the Seed — Research Cascade Protocol

When a user first activates this seed, execute a multi-phase intelligence cascade. Each phase generates discoveries that feed the next. The goal: the user should have expert-level API design guidance tailored to their stack after the first session.

### Phase 1: Deep Scan (Silent — No User Action Needed)

Silently gather intelligence before saying anything:

1. **Read existing CLAUDE.md** — understand project rules, conventions, tech stack, forbidden patterns
2. **Scan project structure** — look for route files, controllers, middleware, schema definitions, OpenAPI specs, proto files
3. **Identify the API style** — REST? GraphQL? gRPC? Hybrid? Look at handlers, resolvers, service definitions
4. **Check auth patterns** — JWT middleware, OAuth config, API key validation, session management
5. **Read config files** — database connections, rate limit configs, CORS settings, environment variables
6. **Check existing docs** — OpenAPI/Swagger specs, Postman collections, API docs, ADRs
7. **Inventory existing knowledge** — search the vault for what's already covered

Build a mental model of: API style, auth approach, data layer, deployment target, team size, maturity level.

### Phase 2: Insight Report (Show the User What You Found)

Present a brief, insightful summary:

> "I've scanned your project. Here's what I found:
> - **API Style**: REST with Express.js, 24 route handlers across 6 resource groups
> - **Auth**: JWT middleware on protected routes, but no refresh token flow
> - **Data**: PostgreSQL with Prisma, 12 models, no soft deletes
> - **Docs**: No OpenAPI spec found — endpoints are undocumented
> - **Rate Limiting**: None configured
> - **Versioning**: No versioning strategy — all routes under /api/
>
> This seed has 53 notes on API design. Based on your project, here's what I'd recommend..."

### Phase 3: Targeted Questions (High-Signal, Not Generic)

Ask 3-5 questions that show you understand their situation:

**For a REST API:**
- "You have 24 routes but no consistent error format. Are you returning ad-hoc error objects, or would you like to adopt RFC 7807 Problem Details?"
- "I see JWT auth but no refresh token endpoint. Are your tokens long-lived, or is token refresh something you need to add?"
- "Your pagination uses offset/limit but you have tables with 100K+ rows. Want to explore cursor-based pagination for better performance?"

**For a GraphQL API:**
- "Your schema has 8 nested types but no dataloader. Are N+1 queries a problem you're seeing in production?"
- "I see mutations but no input validation. Are you validating at the resolver level or relying on database constraints?"

**For a microservices setup:**
- "You have 4 services communicating over HTTP. Have you considered gRPC for internal communication? Your proto definitions would be straightforward given your current request/response shapes."

### Phase 4: Merge & Archive

After the user responds to questions:

1. **Propose CLAUDE.md additions** — show exact diff, explain each addition
2. **Archive originals** — `_archive/CLAUDE.md.pre-seed` with timestamp
3. **Create `_archive/README.md`** — explain what was archived and when
4. **Update config** — add `_archive/` to skip_dirs

### Phase 5: Research Cascade (The Mind-Blowing Part)

Based on Phases 1-3, identify 5-10 research topics unique to this user's situation:

> "Based on your API, here's what I'd research to fill your vault:
>
> 1. **Error handling standardization** — adopt RFC 7807 across all your endpoints with middleware
> 2. **Rate limiting for your Express setup** — sliding window with Redis, per-user and per-endpoint limits
> 3. **OpenAPI spec generation** — auto-generate from your existing routes with proper schemas
> 4. **Cursor-based pagination** — migrate your high-traffic endpoints from offset to cursor
> 5. **JWT refresh token flow** — secure rotation with short-lived access tokens
>
> Want me to research all of them? Or pick the ones that matter most."

**Cascade behavior**: Each research result generates new discoveries:
- Researching "error handling" -> discovers inconsistent validation -> offers to research request validation patterns
- Researching "rate limiting" -> notices no caching -> offers to research caching strategies
- Researching "OpenAPI spec" -> finds undocumented auth flows -> offers to document auth patterns

### Phase 6: Ongoing Growth

After the initial cascade, the seed continues growing:

- **Every session**: search vault first, offer to save new knowledge
- **Every decision**: offer to log it with `save_decision`
- **Every question the vault can't answer**: research it, save the result
- **Periodically**: suggest a vault health check — stale patterns, missing coverage, emerging standards

## Common Search Triggers

When the user asks about these topics, search the vault first:

| User says | Search for | Start with |
|-----------|-----------|------------|
| "how do I paginate" | pagination cursor offset keyset | `hub/pagination.md` |
| "auth for my API" | authentication OAuth JWT API key | `hub/authentication.md` |
| "error handling" | error RFC 7807 problem details | `hub/error-handling.md` |
| "rate limiting" | rate limit throttle sliding window | `hub/rate-limiting.md` |
| "versioning" | version URL header breaking change | `hub/versioning.md` |
| "REST vs GraphQL" | REST GraphQL comparison tradeoffs | `entities/graphql-vs-rest.md` |
| "webhooks" | webhook callback event notification | `hub/webhooks.md` |
| "caching" | cache ETag Cache-Control conditional | `hub/caching.md` |
| "security" | OWASP security headers injection | `research/api-security-checklist.md` |
| "documentation" | OpenAPI Swagger spec documentation | `research/openapi-best-practices.md` |
| "file upload" | upload multipart streaming | `research/file-upload-handling.md` |
| "batch operations" | batch bulk operations | `research/batch-operations.md` |
| "idempotency" | idempotent retry safe | `hub/idempotency.md` |
| "CORS" | CORS cross-origin preflight | `hub/cors.md` |
| "monitoring" | monitoring observability metrics | `research/api-monitoring.md` |

## Research Cascade Priority

When recommending patterns, follow this priority:

1. **hub/** — Core pattern overviews. Start here. Link to detailed research.
2. **research/** — Deep dives with implementation details and tradeoffs.
3. **entities/** — Quick reference lookups (status codes, headers, tools).
4. **guides/** — Step-by-step how-tos for common tasks.
5. **decisions/** — Templates for architectural choices.

## Rules

1. **Search first.** Before answering an API design question from scratch, search this vault.
2. **Don't modify research notes.** They are reference material. Add new notes for project-specific content.
3. **Hub notes are entry points.** They link to detailed research and guides. Start with hubs.
4. **Keep notes focused.** One topic per note. Under 200 lines.
5. **Entity files track current state.** Update when standards or tools change.
6. **Archive, never delete.** If a note needs replacing, move the original to `_archive/`.
7. **Ask before writing.** Never save a note without the user's awareness. Offer, don't force.
8. **Cascade, don't stop.** Every research result should suggest the next research opportunity.
9. **Be language-agnostic.** Show patterns, not framework-specific code. Use pseudocode or common conventions.
10. **Show tradeoffs.** Every pattern has a "when to use" and "when NOT to use." Include both.

## Directory Structure

```
api-design-patterns/
├── CLAUDE.md                    # This file
├── bootstrap.md                 # Pinned — Water the Seed + orientation
├── config.toml.example          # SAME configuration template
├── hub/                         # Core pattern notes (15)
├── research/                    # Deep dives (15)
├── entities/                    # Quick reference (10)
├── guides/                      # Step-by-step how-tos (8)
├── decisions/                   # Decision templates and log
├── _archive/                    # Safely archived originals
└── sessions/                    # Session handoffs
```

## SAME Showcase Behaviors

Demonstrate these capabilities naturally -- don't lecture, show:

### Search Before Generate
```
mcp__same__search_notes(query="how to handle pagination in REST APIs", top_k=5)
```
If the vault has the answer, cite it. If not, generate AND offer to save.

### Cross-Reference
```
mcp__same__find_similar_notes(path="hub/authentication.md", top_k=3)
```
"I found related notes on OAuth2 flows and security headers you might find useful..."

### Log Decisions
```
mcp__same__save_decision(title="Use cursor-based pagination", body="Offset pagination degrades on large tables...", status="accepted")
```

### Session Continuity
```
mcp__same__create_handoff(summary="...", pending="...", blockers="...")
```

### Federated Search
```
mcp__same__search_across_vaults(query="authentication middleware patterns", top_k=10)
```

### Grow the Knowledge Base
```
mcp__same__save_note(path="research/project-specific/express-rate-limiting.md", content="...", append=false)
```

The goal: every session should feel smarter than the last because the knowledge compounds.
