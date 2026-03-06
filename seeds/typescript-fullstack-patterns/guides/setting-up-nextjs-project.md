---
title: "Setting Up a New Next.js Project (The Right Way)"
tags: [guide, nextjs, setup, project, typescript, tailwind, getting-started]
content_type: guide
domain: typescript-fullstack
---

# Setting Up a New Next.js Project (The Right Way)

## Step 1: Create the Project

```bash
pnpm create next-app@latest my-app --typescript --tailwind --eslint --app --src-dir --import-alias "@/*"
cd my-app
```

## Step 2: Tighten TypeScript

```json
// tsconfig.json — add strict options
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true
  }
}
```

## Step 3: Add Core Dependencies

```bash
# Database
pnpm add @prisma/client
pnpm add -D prisma

# Validation
pnpm add zod

# Environment variables
pnpm add @t3-oss/env-nextjs

# UI utilities
pnpm add clsx tailwind-merge class-variance-authority
pnpm add sonner  # Toast notifications

# Dev tooling
pnpm add -D prettier prettier-plugin-tailwindcss
```

## Step 4: Set Up Utility Functions

```typescript
// src/lib/utils.ts
import { clsx, type ClassValue } from "clsx";
import { twMerge } from "tailwind-merge";

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}
```

## Step 5: Set Up Environment Validation

Create `src/env.ts` following the pattern in `entities/env-variables-patterns.md`.

## Step 6: Set Up Database

```bash
npx prisma init --datasource-provider postgresql
```

Create the Prisma singleton in `src/lib/db.ts` (see `hub/database-patterns.md`).

## Step 7: Set Up Prettier

Create `.prettierrc` following `entities/eslint-prettier-config.md`.

```bash
pnpm add -D husky lint-staged
npx husky init
echo "npx lint-staged" > .husky/pre-commit
```

## Step 8: Project Structure

```
src/
  app/
    (marketing)/         # Public pages
      page.tsx           # Landing page
      layout.tsx         # Marketing layout
    (app)/               # Authenticated pages
      dashboard/
        page.tsx
      layout.tsx         # App layout (sidebar, nav)
    api/
      health/route.ts    # Health check
    layout.tsx           # Root layout
    globals.css
  components/
    ui/                  # Reusable UI (button, input, card)
    layout/              # Layout components (sidebar, nav)
  lib/
    db.ts                # Prisma singleton
    utils.ts             # Utility functions
  env.ts                 # Environment validation
```

## Step 9: Git Setup

```bash
git init
echo ".env\n.env.local\n.env.*.local" >> .gitignore
```

Create `.env.example` with all required variables (no values).

## Step 10: Verify

```bash
pnpm dev          # Should start without errors
pnpm build        # Should build cleanly
pnpm lint         # Should pass
pnpm typecheck    # If you added the script
```

## See Also

- `hub/nextjs-app-router.md` — Routing patterns
- `hub/tailwind-architecture.md` — Styling setup
- `entities/eslint-prettier-config.md` — Full ESLint/Prettier config
- `entities/package-json-scripts.md` — Recommended scripts
