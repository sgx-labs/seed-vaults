---
title: "Tailwind CSS Architecture"
tags: [tailwind, css, design-tokens, cva, component-variants, responsive, styling, design-system]
content_type: hub
domain: typescript-fullstack
---

# Tailwind CSS Architecture

## Overview

Tailwind CSS in a production app needs structure. Without it, you get inconsistent spacing, duplicated color values, and components with 30-class strings that nobody can read. This note covers design tokens, component variants with CVA, responsive patterns, and how to build a maintainable design system.

## Design Tokens in Tailwind v4

Tailwind v4 uses CSS-first configuration. Define tokens as CSS variables.

```css
/* app/globals.css */
@import "tailwindcss";

@theme {
  --color-brand-50: #eff6ff;
  --color-brand-500: #3b82f6;
  --color-brand-600: #2563eb;
  --color-brand-700: #1d4ed8;

  --color-surface: #ffffff;
  --color-surface-elevated: #f9fafb;
  --color-border: #e5e7eb;

  --radius-sm: 0.375rem;
  --radius-md: 0.5rem;
  --radius-lg: 0.75rem;

  --spacing-page: 1.5rem;
  --spacing-section: 2.5rem;
}
```

```typescript
// Now usable as utilities
<div className="bg-surface rounded-md p-page">
  <section className="mb-section">...</section>
</div>
```

## Component Variants with CVA

Class Variance Authority (CVA) creates type-safe variant systems. The standard for component libraries in Tailwind.

```typescript
// components/ui/button.tsx
import { cva, type VariantProps } from "class-variance-authority";
import { cn } from "@/lib/utils";

const buttonVariants = cva(
  // Base classes — always applied
  "inline-flex items-center justify-center rounded-md font-medium transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-offset-2 disabled:pointer-events-none disabled:opacity-50",
  {
    variants: {
      variant: {
        default: "bg-brand-600 text-white hover:bg-brand-700",
        secondary: "bg-surface-elevated text-gray-900 border border-border hover:bg-gray-100",
        destructive: "bg-red-600 text-white hover:bg-red-700",
        ghost: "hover:bg-gray-100",
        link: "text-brand-600 underline-offset-4 hover:underline",
      },
      size: {
        sm: "h-8 px-3 text-sm",
        md: "h-10 px-4",
        lg: "h-12 px-6 text-lg",
        icon: "h-10 w-10",
      },
    },
    defaultVariants: {
      variant: "default",
      size: "md",
    },
  }
);

interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {}

export function Button({ className, variant, size, ...props }: ButtonProps) {
  return (
    <button className={cn(buttonVariants({ variant, size }), className)} {...props} />
  );
}
```

```typescript
// Usage — fully type-safe
<Button variant="destructive" size="sm">Delete</Button>
<Button variant="ghost" size="icon"><TrashIcon /></Button>
```

## The `cn` Utility

Merge Tailwind classes with conflict resolution using `tailwind-merge`.

```typescript
// lib/utils.ts
import { clsx, type ClassValue } from "clsx";
import { twMerge } from "tailwind-merge";

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}

// Resolves conflicts correctly
cn("px-4 py-2", "px-6") // => "px-6 py-2" (not "px-4 py-2 px-6")
cn("text-red-500", condition && "text-blue-500") // conditional classes
```

## Responsive Patterns

```typescript
// Mobile-first — add complexity as screen grows
<div className="
  grid grid-cols-1       /* Mobile: single column */
  md:grid-cols-2         /* Tablet: two columns */
  lg:grid-cols-3         /* Desktop: three columns */
  gap-4 md:gap-6         /* Responsive spacing */
">

// Container pattern
<div className="mx-auto w-full max-w-7xl px-4 sm:px-6 lg:px-8">
  {children}
</div>
```

## Dark Mode

```typescript
// app/globals.css — with Tailwind v4
@custom-variant dark (&:where(.dark, .dark *));

// Components use dark: prefix
<div className="bg-white dark:bg-gray-900 text-gray-900 dark:text-gray-100">

// Theme toggle with next-themes
"use client";
import { useTheme } from "next-themes";

export function ThemeToggle() {
  const { theme, setTheme } = useTheme();
  return (
    <button onClick={() => setTheme(theme === "dark" ? "light" : "dark")}>
      {theme === "dark" ? <SunIcon /> : <MoonIcon />}
    </button>
  );
}
```

## Animation Patterns

```typescript
// Tailwind built-in animations
<div className="animate-pulse bg-gray-200 h-4 rounded" /> // Skeleton

// Custom animations via CSS
@theme {
  --animate-slide-in: slide-in 0.2s ease-out;
}

@keyframes slide-in {
  from { transform: translateX(-100%); opacity: 0; }
  to { transform: translateX(0); opacity: 1; }
}
```

## Common Mistakes

1. **String concatenation for conditional classes** — use `cn()` or `clsx`, not template literals
2. **Not using design tokens** — hardcoded colors everywhere leads to inconsistency
3. **Overly specific responsive classes** — start mobile-first, add breakpoints only when needed
4. **Huge className strings** — extract to CVA variants or component abstractions
5. **Fighting Tailwind** — if you're writing custom CSS for everything, Tailwind might not be the right choice

## See Also

- `hub/performance-patterns.md` — CSS bundle optimization
- `guides/setting-up-nextjs-project.md` — Tailwind setup in new projects
