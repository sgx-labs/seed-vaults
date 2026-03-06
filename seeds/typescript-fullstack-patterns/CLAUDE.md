---
title: "TypeScript Fullstack Patterns — Governance"
content_type: hub
tags: [governance, typescript, fullstack, seed, rules, research-cascade]
pinned: true
domain: typescript-fullstack
---

# TypeScript Fullstack Patterns — Governance

## Purpose

Knowledge seed for building production TypeScript fullstack applications with Next.js, React, Prisma/Drizzle, and modern tooling. Search before generating -- the answer is likely already here.

## Water the Seed — Research Cascade Protocol

When a user first activates this seed, execute a multi-phase intelligence cascade. Each phase generates discoveries that feed the next. The goal: the user should have expert-level fullstack guidance tailored to their stack after the first session.

### Phase 1: Deep Scan (Silent — No User Action Needed)

Silently gather intelligence before saying anything:

1. **Read existing CLAUDE.md** — understand project rules, conventions, tech stack, forbidden patterns
2. **Scan package.json** — dependencies, dev dependencies, scripts, engine requirements
3. **Read tsconfig.json** — strict mode settings, path aliases, target, module resolution
4. **Check Next.js config** — next.config.js/ts, app vs pages router, experimental features
5. **Scan Prisma schema or Drizzle config** — models, relations, database provider, migrations
6. **Check auth setup** — NextAuth config, Clerk middleware, Lucia setup, auth utilities
7. **Read .env.example / .env.local** — required services, API keys, feature flags
8. **Scan project structure** — app/ vs pages/, src/ layout, component organization, shared packages
9. **Check for monorepo** — turbo.json, pnpm-workspace.yaml, packages/ directory
10. **Inventory existing knowledge** — search the vault for what's already covered

Build a mental model of: framework version, router type, database, auth provider, styling approach, deployment target, monorepo vs single app.

### Phase 2: Insight Report (Show the User What You Found)

Present a brief, insightful summary:

> "I've scanned your project. Here's what I found:
> - **Framework**: Next.js 15 with App Router, React 19, TypeScript 5.6 strict mode
> - **Database**: Prisma 6.x with PostgreSQL, 18 models, no soft deletes
> - **Auth**: NextAuth v5 with GitHub + Google providers, no role-based access
> - **Styling**: Tailwind CSS v4 with custom design tokens, no component library
> - **State**: React Query for server state, Zustand for client state
> - **Testing**: Vitest configured but only 12 test files, no E2E tests
> - **Deployment**: Vercel with edge middleware, no preview environment protection
>
> This seed has 60+ notes on fullstack TypeScript patterns. Based on your project, here's what I'd recommend..."

### Phase 3: Targeted Questions (High-Signal, Not Generic)

Ask 3-5 questions that show you understand their situation:

**For a Next.js App Router project:**
- "You're using `generateStaticParams` in 3 routes but `revalidate` is set to 0 — are you intentionally bypassing the cache, or would ISR with a 60-second revalidation work better?"
- "Your Prisma schema has 18 models but no indexes beyond primary keys. Are you seeing slow queries on the `tasks` or `comments` tables?"
- "I see NextAuth v5 but no middleware protecting routes. Are you checking auth in every page component, or should we add middleware-based route protection?"

**For a monorepo setup:**
- "Your `packages/ui` exports 24 components but has no barrel file optimization. Are you seeing slow builds from re-exporting everything?"
- "I see three apps sharing a `packages/db` — are all three connecting to the same database, or do you have per-app schemas?"

**For a project with testing gaps:**
- "You have Vitest configured but no MSW handlers. Are you mocking API calls manually in each test, or skipping API tests entirely?"

### Phase 4: Merge & Archive

After the user responds to questions:

1. **Propose CLAUDE.md additions** — show exact diff, explain each addition
2. **Archive originals** — `_archive/CLAUDE.md.pre-seed` with timestamp
3. **Create `_archive/README.md`** — explain what was archived and when
4. **Update config** — add `_archive/` to skip_dirs

### Phase 5: Research Cascade (The Mind-Blowing Part)

Based on Phases 1-3, identify 5-10 research topics unique to this user's situation:

> "Based on your stack, here's what I'd research to fill your vault:
>
> 1. **Server Component data flow** — your RSC/client boundary is inconsistent, let's establish clear patterns
> 2. **Prisma query optimization** — add indexes, use `select` instead of `include` for your hot paths
> 3. **NextAuth middleware protection** — protect routes at the edge instead of per-page checks
> 4. **Zod schema sharing** — unify your API validation and form validation with shared schemas
> 5. **E2E testing setup** — Playwright with auth fixtures for your NextAuth setup
> 6. **Tailwind design system** — extract your repeated patterns into a component variant system
>
> Want me to research all of them? Or pick the ones that matter most."

**Cascade behavior**: Each research result generates new discoveries:
- Researching "RSC data flow" -> discovers prop drilling -> offers to research component composition patterns
- Researching "Prisma optimization" -> notices N+1 queries -> offers to research dataloader patterns
- Researching "testing setup" -> finds untested API routes -> offers to research API integration testing

### Phase 6: Ongoing Growth

After the initial cascade, the seed continues growing:

- **Every session**: search vault first, offer to save new knowledge
- **Every decision**: offer to log it with `save_decision`
- **Every question the vault can't answer**: research it, save the result
- **Periodically**: suggest a vault health check — stale patterns, missing coverage, new framework versions

## Common Search Triggers

When the user asks about these topics, search the vault first:

| User says | Search for | Start with |
|-----------|-----------|------------|
| "TypeScript types" | branded types discriminated unions const | `hub/typescript-strict-patterns.md` |
| "server components" | RSC server client boundary | `hub/react-server-components.md` |
| "app router" | layout loading error parallel routes | `hub/nextjs-app-router.md` |
| "database" | Prisma Drizzle migration transaction | `hub/database-patterns.md` |
| "auth" | NextAuth Clerk Lucia authentication | `hub/authentication-patterns.md` |
| "state management" | Zustand React Query URL state | `hub/state-management.md` |
| "forms" | React Hook Form Zod server actions | `hub/form-handling.md` |
| "errors" | Result type error boundary toast | `hub/error-handling.md` |
| "API routes" | route handler middleware validation | `hub/api-route-patterns.md` |
| "styling" | Tailwind design tokens variants | `hub/tailwind-architecture.md` |
| "testing" | Vitest Playwright MSW test | `hub/testing-strategies.md` |
| "deploy" | Vercel Railway Docker edge | `hub/deployment-patterns.md` |
| "monorepo" | Turborepo packages shared | `hub/monorepo-setup.md` |
| "real-time" | WebSocket SSE polling Supabase | `hub/realtime-patterns.md` |
| "performance" | Core Web Vitals bundle lazy | `hub/performance-patterns.md` |

## Research Cascade Priority

When recommending patterns, follow this priority:

1. **hub/** — Core pattern overviews. Start here. Link to detailed research.
2. **research/** — Deep dives with implementation details and tradeoffs.
3. **entities/** — Quick reference lookups (utility types, hooks, conventions).
4. **guides/** — Step-by-step how-tos for common tasks.
5. **decisions/** — Templates for architectural choices.

## Rules

1. **Search first.** Before answering a fullstack question from scratch, search this vault.
2. **Don't modify research notes.** They are reference material. Add new notes for project-specific content.
3. **Hub notes are entry points.** They link to detailed research and guides. Start with hubs.
4. **Keep notes focused.** One topic per note. Under 200 lines.
5. **Entity files track current state.** Update when frameworks or tools change.
6. **Archive, never delete.** If a note needs replacing, move the original to `_archive/`.
7. **Ask before writing.** Never save a note without the user's awareness. Offer, don't force.
8. **Cascade, don't stop.** Every research result should suggest the next research opportunity.
9. **TypeScript-first.** Show proper types everywhere. Never use `any`. Show both simple and production versions.
10. **Show tradeoffs.** Every pattern has a "when to use" and "when NOT to use." Include both.

## Directory Structure

```
typescript-fullstack-patterns/
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
mcp__same__search_notes(query="how to handle server components data fetching", top_k=5)
```
If the vault has the answer, cite it. If not, generate AND offer to save.

### Cross-Reference
```
mcp__same__find_similar_notes(path="hub/form-handling.md", top_k=3)
```
"I found related notes on Zod schemas and server actions you might find useful..."

### Log Decisions
```
mcp__same__save_decision(title="Use Drizzle over Prisma for edge runtime", body="Prisma requires a data proxy for edge. Drizzle works natively...", status="accepted")
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
mcp__same__save_note(path="research/project-specific/nextauth-discord-provider.md", content="...", append=false)
```

The goal: every session should feel smarter than the last because the knowledge compounds.
