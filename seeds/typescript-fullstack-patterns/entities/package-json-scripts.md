---
title: "Package.json Scripts Reference"
tags: [package-json, scripts, npm, pnpm, reference, entity, tooling]
content_type: entity
domain: typescript-fullstack
---

# Package.json Scripts Reference

## Standard Next.js Scripts

```json
{
  "scripts": {
    "dev": "next dev --turbopack",
    "build": "next build",
    "start": "next start",
    "lint": "next lint",
    "lint:fix": "next lint --fix"
  }
}
```

## Full Production Setup

```json
{
  "scripts": {
    "dev": "next dev --turbopack",
    "build": "next build",
    "start": "next start",
    "lint": "next lint",
    "lint:fix": "next lint --fix",
    "format": "prettier --write .",
    "format:check": "prettier --check .",
    "typecheck": "tsc --noEmit",
    "test": "vitest",
    "test:ui": "vitest --ui",
    "test:coverage": "vitest run --coverage",
    "test:e2e": "playwright test",
    "test:e2e:ui": "playwright test --ui",
    "db:generate": "prisma generate",
    "db:migrate": "prisma migrate dev",
    "db:push": "prisma db push",
    "db:studio": "prisma studio",
    "db:seed": "prisma db seed",
    "db:reset": "prisma migrate reset",
    "email:dev": "email dev --dir emails",
    "analyze": "ANALYZE=true next build",
    "check": "pnpm lint && pnpm typecheck && pnpm test run",
    "prepare": "husky"
  }
}
```

## Script Patterns

| Script | Purpose |
|--------|---------|
| `check` | Run all checks before commit/push |
| `prepare` | Auto-run on `pnpm install` (sets up git hooks) |
| `postinstall` | Run after install (e.g., `prisma generate`) |
| `predev` | Run before dev server starts |

## Monorepo Scripts (Turborepo)

```json
{
  "scripts": {
    "dev": "turbo dev",
    "build": "turbo build",
    "lint": "turbo lint",
    "test": "turbo test",
    "db:push": "pnpm --filter @repo/db db:push",
    "db:studio": "pnpm --filter @repo/db studio"
  }
}
```

## See Also

- `hub/testing-strategies.md` — Test scripts
- `hub/monorepo-setup.md` — Monorepo scripts
- `entities/eslint-prettier-config.md` — Lint/format config
