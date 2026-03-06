---
title: "Next.js App Router Patterns"
tags: [nextjs, app-router, layouts, loading, error-boundaries, parallel-routes, intercepting-routes, routing]
content_type: hub
domain: typescript-fullstack
---

# Next.js App Router Patterns

## Overview

The App Router (introduced in Next.js 13.4, stable since 14) replaces the Pages Router with a file-convention-based system built on React Server Components. Layouts are nested, loading states are automatic, and data fetching happens at the component level. This note covers the patterns that matter in production.

## Layouts — Nested and Persistent

Layouts wrap their children and persist across navigations. They do NOT re-render when the child route changes.

```typescript
// app/dashboard/layout.tsx
export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <div className="flex">
      <Sidebar /> {/* Persists across /dashboard/* navigations */}
      <main className="flex-1">{children}</main>
    </div>
  );
}
```

**Footgun**: Layouts don't receive the current pathname as a prop. If your sidebar needs to highlight the active route, use `usePathname()` in a Client Component.

**Footgun**: Layouts don't re-fetch data on child navigation. If your layout fetches user data, it won't refresh when the user navigates between dashboard pages. For data that must stay fresh, fetch it in a Client Component with React Query or use `revalidatePath`.

## Loading States

A `loading.tsx` file creates an instant loading state using React Suspense.

```typescript
// app/dashboard/loading.tsx
export default function DashboardLoading() {
  return <DashboardSkeleton />;
}

// This is equivalent to wrapping the page in Suspense:
// <Suspense fallback={<DashboardSkeleton />}>
//   <DashboardPage />
// </Suspense>
```

**Production pattern** — use multiple Suspense boundaries for streaming:

```typescript
// app/dashboard/page.tsx
export default function Dashboard() {
  return (
    <>
      <h1>Dashboard</h1>
      <Suspense fallback={<StatsSkeleton />}>
        <StatsPanel /> {/* Fetches its own data, streams in */}
      </Suspense>
      <Suspense fallback={<ActivitySkeleton />}>
        <ActivityFeed /> {/* Independent stream */}
      </Suspense>
    </>
  );
}
```

## Error Boundaries

`error.tsx` catches errors in a route segment and its children. Must be a Client Component.

```typescript
"use client";

export default function DashboardError({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  useEffect(() => {
    // Log to error reporting service
    captureException(error);
  }, [error]);

  return (
    <div>
      <h2>Something went wrong</h2>
      <p>{error.message}</p>
      <button onClick={() => reset()}>Try again</button>
    </div>
  );
}
```

**Footgun**: `error.tsx` does NOT catch errors in the same segment's `layout.tsx`. To catch layout errors, place `error.tsx` in the parent segment. For root layout errors, use `global-error.tsx`.

## Parallel Routes

Render multiple pages simultaneously in the same layout. Named slots use the `@folder` convention.

```typescript
// app/dashboard/layout.tsx
export default function DashboardLayout({
  children,
  analytics,
  notifications,
}: {
  children: React.ReactNode;
  analytics: React.ReactNode;
  notifications: React.ReactNode;
}) {
  return (
    <div>
      {children}
      <div className="grid grid-cols-2 gap-4">
        {analytics}      {/* app/dashboard/@analytics/page.tsx */}
        {notifications}  {/* app/dashboard/@notifications/page.tsx */}
      </div>
    </div>
  );
}
```

**When to use**: Dashboards with independent sections, modals that preserve URL state, conditional rendering based on auth.

## Intercepting Routes

Intercept a route to show it in a different context (e.g., modal) while preserving the full-page route for direct navigation.

```
app/
  feed/
    page.tsx                    # Feed page
    (..)photo/[id]/page.tsx     # Intercept: show photo in modal
  photo/
    [id]/page.tsx               # Direct: full photo page
```

**Pattern**: Instagram-style feed where clicking a photo opens a modal, but sharing the URL loads the full page.

## Route Groups

Organize routes without affecting the URL. Use `(folder)` convention.

```
app/
  (marketing)/
    page.tsx          # / — marketing homepage
    about/page.tsx    # /about
    layout.tsx        # Marketing layout (no sidebar)
  (app)/
    dashboard/page.tsx   # /dashboard
    settings/page.tsx    # /settings
    layout.tsx           # App layout (with sidebar)
```

## Metadata

```typescript
// Static — export const metadata: Metadata = { title: "Dashboard" };
// Dynamic — export async function generateMetadata({ params }) { ... }
```

## When NOT to Use App Router

- If you need fully static sites with no JS — consider Astro
- If your team is deeply invested in Pages Router patterns — migration has real costs
- If you need stable React 18 features without RSC complexity — Pages Router still works

## Research Notes

- `research/nextjs-caching-deep-dive.md` — Caching behavior, revalidation, cache invalidation gotchas

## See Also

- `hub/react-server-components.md` — RSC vs Client Components
- `entities/nextjs-file-conventions.md` — Complete file convention reference
- `hub/performance-patterns.md` — Streaming, lazy loading, Core Web Vitals
- `guides/setting-up-nextjs-project.md` — New project setup guide
