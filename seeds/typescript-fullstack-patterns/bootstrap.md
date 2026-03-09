---
title: "Bootstrap — New Session Quick Start"
tags: [bootstrap, onboarding, session-start, water-the-seed, research-cascade, orientation, typescript-fullstack]
content_type: hub
domain: typescript-fullstack
pinned: true
---

# Bootstrap — New Session Quick Start

## Water the Seed

First time? Your agent will handle everything. Just run:

```bash
same init && same reindex
```

Then start a conversation. Your agent reads this file automatically and begins the Research Cascade -- a multi-phase process that scans your project, understands your stack, asks smart questions, and fills your vault with personalized knowledge. The more you interact, the smarter it gets.

**What happens next (automatic):**
1. Agent silently scans your project -- package.json, tsconfig, Next.js config, Prisma schema, env files
2. Agent presents an insight report showing what it learned about your stack
3. Agent asks 3-5 targeted questions about your architecture and pain points
4. Agent proposes CLAUDE.md additions (archives originals safely)
5. Agent recommends research topics specific to YOUR stack
6. You pick which topics matter -- agents research in parallel, save results to your vault
7. Each research result suggests new topics -- the vault grows exponentially

After the first session, your vault has generic fullstack patterns PLUS project-specific intelligence that no one else has.

## Returning Sessions

Already set up? Here is what your agent does automatically on every session:

- **Loads context** -- SAME surfaces your pinned notes, recent handoffs, and decisions
- **Remembers where you left off** -- session handoffs capture what was done and what is pending
- **Searches before generating** -- your vault has 60+ curated notes, growing with every session
- **Cross-references** -- finds related patterns you might not know about
- **Compounds knowledge** -- every useful answer gets saved for next time

## Where To Find Things

| Need | Search for | Or read directly |
|------|-----------|-----------------|
| TypeScript patterns | `branded types discriminated unions` | `hub/typescript-strict-patterns.md` |
| App Router | `layout loading error parallel routes` | `hub/nextjs-app-router.md` |
| Server Components | `RSC client boundary data flow` | `hub/react-server-components.md` |
| Database | `Prisma Drizzle migration transaction` | `hub/database-patterns.md` |
| Authentication | `NextAuth Clerk Lucia auth` | `hub/authentication-patterns.md` |
| State management | `Zustand React Query URL state` | `hub/state-management.md` |
| Forms | `React Hook Form Zod server actions` | `hub/form-handling.md` |
| Error handling | `Result type error boundary toast` | `hub/error-handling.md` |
| API routes | `route handler middleware validation` | `hub/api-route-patterns.md` |
| Styling | `Tailwind design tokens variants CVA` | `hub/tailwind-architecture.md` |
| Testing | `Vitest Playwright MSW test` | `hub/testing-strategies.md` |
| Deployment | `Vercel Railway Docker edge` | `hub/deployment-patterns.md` |
| Monorepo | `Turborepo packages shared` | `hub/monorepo-setup.md` |
| Real-time | `WebSocket SSE polling Supabase` | `hub/realtime-patterns.md` |
| Performance | `Core Web Vitals bundle lazy` | `hub/performance-patterns.md` |

## Key Questions This Seed Answers

1. When should I use Server Components vs Client Components?
2. How do I structure App Router layouts for complex navigation?
3. What authentication approach fits my Next.js app?
4. How do I handle forms with client + server validation?
5. Prisma or Drizzle -- which fits my project?
6. How do I implement optimistic UI updates?
7. What state management reduces complexity in fullstack TypeScript?
8. How do I set up a productive monorepo?
9. What testing strategy gives the most confidence?
10. How do I handle real-time features in Next.js?

## Content Organization

- `hub/` -- Core pattern overviews, start here for any topic
- `research/` -- Deep dives with implementation details (grows with each session)
- `entities/` -- Quick reference docs for utility types, hooks, conventions
- `guides/` -- Step-by-step how-tos for common fullstack tasks
- `decisions/` -- Architectural decision templates and log
- `sessions/` -- Session handoffs for continuity
- `_archive/` -- Safely archived originals from merge process
