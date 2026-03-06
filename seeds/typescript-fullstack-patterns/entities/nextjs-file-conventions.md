---
title: "Next.js File Conventions Reference"
tags: [nextjs, file-conventions, app-router, page, layout, loading, error, reference, entity]
content_type: entity
pinned: true
domain: typescript-fullstack
---

# Next.js File Conventions Reference

## Route Files

| File | Purpose | Component Type |
|------|---------|---------------|
| `page.tsx` | Page UI (makes route publicly accessible) | Server (default) |
| `layout.tsx` | Shared UI for a segment and its children | Server (default) |
| `loading.tsx` | Loading UI (wraps page in Suspense) | Server (default) |
| `error.tsx` | Error UI (catches errors in segment) | **Client** (required) |
| `global-error.tsx` | Root error UI (catches root layout errors) | **Client** (required) |
| `not-found.tsx` | 404 UI for `notFound()` calls | Server (default) |
| `template.tsx` | Like layout but re-renders on navigation | Server (default) |
| `default.tsx` | Fallback UI for parallel routes | Server (default) |
| `route.ts` | API endpoint (cannot coexist with page.tsx) | N/A |

## Special Directories

| Convention | Purpose | Example |
|------------|---------|---------|
| `app/` | App Router root | Routes defined by nested folders |
| `(group)` | Route group (no URL impact) | `(marketing)/`, `(app)/` |
| `[param]` | Dynamic segment | `[id]/`, `[slug]/` |
| `[...slug]` | Catch-all segment | Matches `/a`, `/a/b`, `/a/b/c` |
| `[[...slug]]` | Optional catch-all | Also matches `/` (root) |
| `@slot` | Parallel route slot | `@modal/`, `@sidebar/` |
| `(.)folder` | Intercept same level | Modal patterns |
| `(..)folder` | Intercept one level up | Feed -> photo modal |
| `_folder` | Private folder (excluded from routing) | `_components/`, `_lib/` |

## Config Files

| File | Purpose |
|------|---------|
| `next.config.ts` | Next.js configuration |
| `middleware.ts` | Request middleware (runs on edge) |
| `instrumentation.ts` | Telemetry/monitoring hook |
| `mdx-components.tsx` | MDX component customization |

## Route Segment Config

Export these from `page.tsx` or `layout.tsx`:

```typescript
// Force dynamic rendering
export const dynamic = "force-dynamic"; // or "auto" | "force-static" | "error"

// Revalidation interval (ISR)
export const revalidate = 60; // seconds (0 = always fresh, false = infinite)

// Runtime selection
export const runtime = "edge"; // or "nodejs" (default)

// Maximum request duration
export const maxDuration = 30; // seconds

// Dynamic params behavior
export const dynamicParams = true; // Allow params not returned by generateStaticParams
```

## Data Fetching Exports

```typescript
// Generate static params for dynamic routes
export async function generateStaticParams() {
  const posts = await getPosts();
  return posts.map((post) => ({ slug: post.slug }));
}

// Generate metadata
export async function generateMetadata({ params }: Props): Promise<Metadata> {
  return { title: "..." };
}
```

## Rendering Order

```
Layout -> Template -> Error Boundary -> Loading -> Not Found -> Page
```

Nesting: parent layout wraps child layout, which wraps child's template, error, loading, and page.

## See Also

- `hub/nextjs-app-router.md` — App Router patterns
- `hub/react-server-components.md` — RSC vs Client Components
