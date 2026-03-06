---
title: "Framework Choice: Next.js vs Remix vs SvelteKit"
tags: [decision, framework, nextjs, remix, sveltekit, comparison]
content_type: decision
domain: typescript-fullstack
---

# Framework Choice: Next.js vs Remix vs SvelteKit

## Decision Template

Use this template when choosing a fullstack framework. Fill in your project's specific requirements.

## Criteria Matrix

| Factor | Next.js | Remix | SvelteKit |
|--------|---------|-------|-----------|
| **React ecosystem** | Full access | Full access | Svelte (different) |
| **SSR/SSG/ISR** | All three | SSR + streaming | All three |
| **Server Components** | Yes (RSC) | No | N/A (Svelte approach) |
| **Deployment** | Vercel (best), anywhere | Anywhere equally | Anywhere equally |
| **Learning curve** | Medium | Medium | Lower (Svelte) |
| **Bundle size** | Large (React) | Large (React) | Smaller (Svelte) |
| **Data loading** | RSC + fetch | Loaders (Web standards) | load() functions |
| **Mutations** | Server Actions | Actions (Form-based) | form actions |
| **Ecosystem size** | Largest | Medium | Growing |
| **Edge runtime** | Yes | Yes | Yes |
| **TypeScript** | Excellent | Excellent | Excellent |
| **Hiring pool** | Largest (React) | Medium (React) | Smaller |

## Decision Framework

### Choose Next.js if:
- Your team knows React
- You want the largest ecosystem (components, libraries, examples)
- You need ISR/static generation at scale
- You're OK with Vercel's deployment model (or can self-host)
- You want React Server Components

### Choose Remix if:
- You prefer Web standard patterns (FormData, Request/Response)
- You want framework-agnostic deployment (no vendor preference)
- You need excellent nested routing with data loading
- You prefer progressive enhancement (forms work without JS)
- You want simpler mental model (no RSC complexity)

### Choose SvelteKit if:
- Your team is OK learning Svelte (or prefers it)
- Bundle size and performance are top priorities
- You want a simpler reactivity model (no hooks, no virtual DOM)
- You're building a smaller team project
- You want built-in transitions and animations

## Your Decision

```
## AD-001: Framework Choice

**Status:** [Proposed/Accepted]
**Date:** YYYY-MM-DD

**Decision:** [Which framework and why]

**Key factors:**
1. [Most important reason]
2. [Second reason]
3. [Third reason]

**Alternative considered:** [What you didn't choose and why]

**Risks:** [Known risks with this choice]
```

## See Also

- `hub/nextjs-app-router.md` — Next.js patterns
- `hub/react-server-components.md` — RSC patterns
- `hub/deployment-patterns.md` — Deployment per framework
