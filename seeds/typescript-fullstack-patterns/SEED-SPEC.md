---
title: "TypeScript Fullstack Patterns — Seed Spec"
tags: [seed-spec, planning, structure, quality-checklist, typescript-fullstack]
content_type: hub
domain: typescript-fullstack
---

# TypeScript Fullstack Patterns — Seed Spec

## Metadata
- **Name**: typescript-fullstack-patterns
- **Type**: Knowledge
- **Audience**: TypeScript fullstack developers building with Next.js, React, and modern tooling
- **Note count target**: 55-65
- **Agent build time**: 4-6 hours

## Purpose

Comprehensive reference for TypeScript fullstack developers who need production-ready patterns. Covers TypeScript strict mode, Next.js App Router, React Server Components, database patterns, authentication, state management, forms, testing, deployment, and real-time features. Every note should help a developer ship better code or avoid a production incident.

## Hub Categories

1. hub/typescript-strict-patterns.md — Const assertions, branded types, discriminated unions, type narrowing
2. hub/nextjs-app-router.md — Layouts, loading states, error boundaries, parallel routes, intercepting routes
3. hub/react-server-components.md — RSC vs Client Components, data flow, serialization boundaries
4. hub/database-patterns.md — Prisma/Drizzle migrations, relations, transactions, edge cases
5. hub/authentication-patterns.md — NextAuth/Auth.js, Clerk, Lucia tradeoffs and patterns
6. hub/state-management.md — Server state vs client state, Zustand, React Query, URL state
7. hub/form-handling.md — React Hook Form + Zod, server actions, optimistic updates
8. hub/error-handling.md — Result types, error boundaries, toast notifications, graceful degradation
9. hub/api-route-patterns.md — Route handlers, middleware, validation, rate limiting
10. hub/tailwind-architecture.md — Design tokens, component variants, responsive patterns, CVA
11. hub/testing-strategies.md — Vitest, Playwright, MSW, what to test at each layer
12. hub/deployment-patterns.md — Vercel, Railway, Docker, edge functions, preview deployments
13. hub/monorepo-setup.md — Turborepo, shared packages, versioning, dependency management
14. hub/realtime-patterns.md — WebSocket, SSE, polling, Supabase Realtime
15. hub/performance-patterns.md — Lazy loading, image optimization, bundle analysis, Core Web Vitals

## Key Questions (agent research targets)

1. When should I use a Server Component vs a Client Component?
2. How do I structure Next.js App Router layouts for complex navigation?
3. What's the best auth solution for my Next.js app?
4. How do I handle forms with validation on both client and server?
5. Prisma or Drizzle -- which should I choose and why?
6. How do I implement optimistic UI updates with server actions?
7. What state management approach reduces complexity in a fullstack app?
8. How do I set up a monorepo that doesn't slow down my team?
9. What testing strategy gives the most confidence per hour invested?
10. How do I handle real-time features in Next.js?
11. What's the production-ready error handling pattern for fullstack TypeScript?
12. How do I deploy a Next.js app beyond Vercel?
13. How do I integrate Stripe payments properly?
14. What security checklist should I follow for a fullstack app?
15. How do I add AI features to my Next.js app?

## Entities

1. entities/typescript-utility-types.md — Built-in utility types reference with examples
2. entities/nextjs-file-conventions.md — App Router file conventions (page, layout, loading, error, etc.)
3. entities/http-status-codes.md — Status codes for API routes with Next.js context
4. entities/prisma-schema-reference.md — Prisma schema patterns quick reference
5. entities/zod-schema-patterns.md — Zod validation patterns and transforms
6. entities/react-hooks-reference.md — Built-in hooks + common custom hooks
7. entities/env-variables-patterns.md — .env files, validation, type-safe access
8. entities/package-json-scripts.md — Common scripts for fullstack TypeScript projects
9. entities/common-middleware-patterns.md — Next.js middleware patterns
10. entities/eslint-prettier-config.md — ESLint + Prettier configuration reference

## Decisions to Pre-Populate

1. Decision log — template and entries
2. Framework choice template — Next.js vs Remix vs SvelteKit
3. Database choice template — PostgreSQL vs MySQL vs SQLite

## Water the Seed Protocol

### 1. Scan & Merge
- Agent reads existing CLAUDE.md before suggesting changes
- Proposes merged version — shows diff, waits for approval
- Archives originals to `_archive/` before writing changes
- Never overwrites user work

### 2. Gap Analysis
- After indexing, compare seed coverage against user's actual stack
- Search vault for each key question — identify what's covered, what's missing
- Factor in user's framework version, database, auth provider, deployment target

### 3. Research Recommendations
- Present numbered list of recommended research topics based on gaps
- Offer to spin up parallel research agents for approved topics
- New research gets saved as notes in the user's vault

### 4. SAME Showcase Behaviors
- Use `same search` to find answers before generating from scratch
- Use `same save_decision` when the user makes an architectural decision
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
