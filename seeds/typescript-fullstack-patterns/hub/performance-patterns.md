---
title: "Performance Patterns"
tags: [performance, core-web-vitals, lazy-loading, image-optimization, bundle-analysis, streaming, suspense]
content_type: hub
domain: typescript-fullstack
---

# Performance Patterns

## Overview

Performance in a fullstack Next.js app is measured by Core Web Vitals: LCP (Largest Contentful Paint), INP (Interaction to Next Paint), and CLS (Cumulative Layout Shift). This note covers the patterns that move these metrics: lazy loading, image optimization, bundle analysis, streaming, and smart caching.

## Core Web Vitals Targets

| Metric | Good | Needs Work | Poor |
|--------|------|------------|------|
| LCP | < 2.5s | 2.5-4.0s | > 4.0s |
| INP | < 200ms | 200-500ms | > 500ms |
| CLS | < 0.1 | 0.1-0.25 | > 0.25 |

## Image Optimization

Next.js `<Image>` handles responsive sizing, lazy loading, and format conversion automatically.

```typescript
import Image from "next/image";

// Static import — best for known images (auto-sized, blur placeholder)
import heroImage from "@/public/hero.jpg";

export function Hero() {
  return (
    <Image
      src={heroImage}
      alt="Hero banner"
      placeholder="blur" // Automatic blur-up from static import
      priority // Above the fold — skip lazy loading
      className="w-full h-auto"
    />
  );
}

// Dynamic images — always set width/height or use fill
export function UserAvatar({ src, name }: { src: string; name: string }) {
  return (
    <Image
      src={src}
      alt={name}
      width={48}
      height={48}
      className="rounded-full"
      sizes="48px" // Tells the browser the rendered size
    />
  );
}
```

**Footgun**: Without `sizes`, Next.js generates images at all breakpoints. For a 48px avatar, that wastes bandwidth. Always set `sizes` for dynamic images.

## Lazy Loading Components

```typescript
import dynamic from "next/dynamic";

// Heavy component loaded on demand
const Chart = dynamic(() => import("@/components/chart"), {
  loading: () => <ChartSkeleton />,
  ssr: false, // Skip server rendering for client-only components
});

// Conditional loading
export function Dashboard() {
  const [showAnalytics, setShowAnalytics] = useState(false);

  return (
    <div>
      <button onClick={() => setShowAnalytics(true)}>Show Analytics</button>
      {showAnalytics && <Chart />} {/* Only loads JS when opened */}
    </div>
  );
}
```

## Bundle Analysis

```json
// package.json
{
  "scripts": {
    "analyze": "ANALYZE=true next build"
  }
}
```

```typescript
// next.config.ts
import withBundleAnalyzer from "@next/bundle-analyzer";

const config = withBundleAnalyzer({
  enabled: process.env.ANALYZE === "true",
})({
  // ... your config
});
export default config;
```

**Common bundle bloaters:**
- `moment` -> replace with `date-fns` or `dayjs`
- `lodash` -> import individual functions: `import debounce from "lodash/debounce"`
- Icon libraries -> import individual icons, not the entire set
- Markdown renderers -> lazy load if not above the fold

## Streaming with Suspense

Don't make the user wait for the slowest data fetch. Stream independent sections.

```typescript
// app/dashboard/page.tsx
import { Suspense } from "react";

export default function Dashboard() {
  return (
    <div className="grid grid-cols-2 gap-4">
      {/* Fast — renders immediately */}
      <Suspense fallback={<Skeleton className="h-32" />}>
        <QuickStats /> {/* 50ms query */}
      </Suspense>

      {/* Slow — streams in when ready */}
      <Suspense fallback={<Skeleton className="h-64" />}>
        <ActivityFeed /> {/* 800ms query */}
      </Suspense>

      {/* Even slower — doesn't block anything else */}
      <Suspense fallback={<Skeleton className="h-48" />}>
        <AnalyticsChart /> {/* 2s query */}
      </Suspense>
    </div>
  );
}
```

## Caching Strategies

```typescript
// Static data — cache until redeployed
const data = await fetch("https://api.example.com/static", {
  cache: "force-cache",
});

// Revalidate periodically
const data = await fetch("https://api.example.com/prices", {
  next: { revalidate: 60 }, // ISR: revalidate every 60 seconds
});

// Always fresh
const data = await fetch("https://api.example.com/realtime", {
  cache: "no-store",
});

// On-demand revalidation (after mutations)
import { revalidatePath, revalidateTag } from "next/cache";

// In a server action
revalidatePath("/dashboard"); // Revalidate specific path
revalidateTag("user-data");   // Revalidate by tag
```

## Preventing CLS

```typescript
// Always set dimensions on images: <Image width={400} height={300} />
// Reserve space for dynamic content: <div className="min-h-[200px]">
// Use CSS aspect-ratio: <div className="aspect-video">
  <iframe src="..." className="w-full h-full" />
</div>
```

## Common Mistakes

1. **Not using `priority` on above-the-fold images** — causes late LCP
2. **Loading everything eagerly** — use `dynamic()` and Suspense for below-the-fold content
3. **No `sizes` prop on Image** — generates unnecessarily large images
4. **Fetching sequentially** — use parallel fetches with `Promise.all` or independent Suspense boundaries
5. **Cache-busting everything** — use `revalidate` instead of `no-store` unless data truly needs to be real-time

## See Also

- `hub/react-server-components.md` — RSC for zero-JS rendering
- `hub/nextjs-app-router.md` — Loading states and streaming
- `research/nextjs-caching-deep-dive.md` — Full caching behavior reference
- `hub/deployment-patterns.md` — CDN and edge caching
