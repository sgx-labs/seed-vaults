---
title: "Next.js App Router Caching Deep Dive"
tags: [nextjs, caching, revalidation, ISR, fetch-cache, router-cache, full-route-cache, data-cache]
content_type: research
domain: typescript-fullstack
---

# Next.js App Router Caching Deep Dive

## The Core Idea

Next.js has four caching layers. Understanding them prevents the #1 App Router complaint: "my data is stale and I don't know why."

## The Four Caches

### 1. Request Memoization (Per-Request)

Same `fetch()` call in multiple components during a single render is deduplicated automatically.

```typescript
// Both components make the same fetch — only ONE network request happens
async function Header() {
  const user = await fetch("/api/user"); // Memoized
  return <nav>{user.name}</nav>;
}

async function Sidebar() {
  const user = await fetch("/api/user"); // Reuses memoized result
  return <aside>{user.avatar}</aside>;
}
```

**Scope**: Single server request only. Does NOT persist between requests.

**Footgun**: Only works with `fetch()`. If you use Prisma directly, wrap with `React.cache()`:

```typescript
import { cache } from "react";

export const getUser = cache(async (id: string) => {
  return db.user.findUnique({ where: { id } });
});
```

### 2. Data Cache (Persistent)

`fetch()` results are cached in a persistent store (survives deployments on Vercel).

```typescript
// Cached forever (until revalidated)
const data = await fetch("https://api.example.com/products", {
  cache: "force-cache", // Default in Next.js 14, opt-in in Next.js 15
});

// Cached for 60 seconds (ISR)
const data = await fetch("https://api.example.com/prices", {
  next: { revalidate: 60 },
});

// Never cached
const data = await fetch("https://api.example.com/stock", {
  cache: "no-store",
});
```

**Next.js 15 change**: Default fetch caching changed from `force-cache` to `no-store`. You must now explicitly opt in to caching.

### 3. Full Route Cache (Build-Time)

Static routes are rendered at build time and cached as HTML + RSC payload.

```typescript
// Static — cached at build time
export default async function AboutPage() {
  return <div>About us</div>; // No dynamic data = fully static
}

// Dynamic — rendered on every request
export default async function DashboardPage() {
  const session = await auth(); // Dynamic function = no caching
  return <div>Welcome {session?.user.name}</div>;
}
```

**Functions that opt out of static caching**: `cookies()`, `headers()`, `searchParams`, `auth()`, `fetch()` with `no-store`.

### 4. Router Cache (Client-Side)

The browser caches visited pages. Navigating back to a previously visited page is instant.

```typescript
// Force fresh data on navigation
import { useRouter } from "next/navigation";

function RefreshButton() {
  const router = useRouter();
  return <button onClick={() => router.refresh()}>Refresh</button>;
}
```

**Duration**: Dynamic pages cached for 30 seconds, static pages for 5 minutes (in Next.js 15).

## Revalidation Strategies

### Time-Based (ISR)

```typescript
// Page-level
export const revalidate = 60; // Revalidate every 60 seconds

// Fetch-level
const data = await fetch(url, { next: { revalidate: 60 } });
```

### On-Demand

```typescript
"use server";

import { revalidatePath, revalidateTag } from "next/cache";

export async function updatePost(id: string, data: PostInput) {
  await db.post.update({ where: { id }, data });

  revalidatePath("/posts");        // Revalidate specific path
  revalidatePath("/posts/[id]", "page"); // Revalidate dynamic route
  revalidateTag("posts");          // Revalidate all fetches tagged "posts"
}

// Tag your fetches
const posts = await fetch("https://api.example.com/posts", {
  next: { tags: ["posts"] },
});
```

## Common Caching Bugs

1. **"My data is stale after mutation"** — call `revalidatePath` or `revalidateTag` in your server action
2. **"Static page shows dynamic data"** — using `cookies()` or `headers()` makes the page dynamic
3. **"Fetch data is cached but I used no-store"** — check if a parent layout or middleware is caching
4. **"Back navigation shows old data"** — Router Cache. Use `router.refresh()` or set `staleTimes` in next.config

```typescript
// next.config.ts — customize router cache
const nextConfig = {
  experimental: {
    staleTimes: {
      dynamic: 0,  // Don't cache dynamic pages in router cache
      static: 180, // Cache static pages for 3 minutes
    },
  },
};
```

## See Also

- `hub/nextjs-app-router.md` — App Router fundamentals
- `hub/react-server-components.md` — Data fetching in RSC
- `hub/performance-patterns.md` — Caching for performance
