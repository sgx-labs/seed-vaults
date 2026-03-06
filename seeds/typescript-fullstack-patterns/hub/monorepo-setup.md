---
title: "Monorepo Setup"
tags: [monorepo, turborepo, pnpm, shared-packages, workspaces, versioning, dependency-management]
content_type: hub
domain: typescript-fullstack
---

# Monorepo Setup

## Overview

A monorepo puts multiple apps and shared packages in one repository. For fullstack TypeScript, this means your web app, API, shared types, UI components, and database schema live together with shared tooling. Turborepo is the standard orchestrator for Next.js monorepos.

## Structure

```
my-project/
  apps/
    web/              # Next.js app
    docs/             # Documentation site
    api/              # Standalone API (if needed)
  packages/
    ui/               # Shared React components
    db/               # Prisma schema + client
    auth/             # Shared auth utilities
    config-eslint/    # Shared ESLint config
    config-typescript/ # Shared tsconfig
  turbo.json
  pnpm-workspace.yaml
  package.json
```

## Workspace Config

```yaml
# pnpm-workspace.yaml
packages:
  - "apps/*"
  - "packages/*"
```

```json
// package.json (root)
{
  "private": true,
  "scripts": {
    "dev": "turbo dev",
    "build": "turbo build",
    "lint": "turbo lint",
    "test": "turbo test",
    "db:push": "pnpm --filter @repo/db db:push",
    "db:studio": "pnpm --filter @repo/db studio"
  },
  "devDependencies": {
    "turbo": "^2.0.0"
  }
}
```

## Turborepo Config

```json
// turbo.json
{
  "$schema": "https://turbo.build/schema.json",
  "tasks": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": [".next/**", "!.next/cache/**", "dist/**"]
    },
    "dev": {
      "cache": false,
      "persistent": true
    },
    "lint": {
      "dependsOn": ["^build"]
    },
    "test": {
      "dependsOn": ["^build"]
    }
  }
}
```

## Shared Database Package

```typescript
// packages/db/package.json
{
  "name": "@repo/db",
  "scripts": {
    "build": "prisma generate",
    "db:push": "prisma db push",
    "studio": "prisma studio"
  },
  "exports": {
    ".": "./src/index.ts",
    "./schema": "./prisma/schema.prisma"
  }
}

// packages/db/src/index.ts
export { PrismaClient } from "@prisma/client";
export * from "@prisma/client";

// Singleton
import { PrismaClient } from "@prisma/client";
const globalForPrisma = globalThis as unknown as { prisma: PrismaClient };
export const db = globalForPrisma.prisma ?? new PrismaClient();
if (process.env.NODE_ENV !== "production") globalForPrisma.prisma = db;
```

## Shared UI Package

```typescript
// packages/ui/package.json
{
  "name": "@repo/ui",
  "exports": {
    "./button": "./src/button.tsx",
    "./card": "./src/card.tsx",
    "./input": "./src/input.tsx"
  }
}

// Usage in apps/web
import { Button } from "@repo/ui/button";
```

**Footgun**: Don't create a barrel file (`index.ts`) that re-exports everything. It kills tree-shaking and slows builds. Use direct imports.

## Shared TypeScript Config

```json
// packages/config-typescript/base.json
{
  "$schema": "https://json.schemastore.org/tsconfig",
  "compilerOptions": {
    "strict": true,
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noUncheckedIndexedAccess": true
  }
}

// apps/web/tsconfig.json
{
  "extends": "@repo/config-typescript/nextjs.json",
  "compilerOptions": {
    "paths": { "@/*": ["./src/*"] }
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx"],
  "exclude": ["node_modules"]
}
```

## Internal Packages vs Published Packages

**Internal** (recommended for most teams): No build step, no version numbers. Turborepo handles dependency ordering. Consumed via workspace protocol.

```json
// apps/web/package.json
{
  "dependencies": {
    "@repo/ui": "workspace:*",
    "@repo/db": "workspace:*"
  }
}
```

**Published**: Use Changesets for versioning and publishing to npm. Only needed if external consumers use your packages.

## Common Mistakes

1. **Building packages before using them** — internal packages don't need a build step if your bundler handles TypeScript
2. **Barrel file re-exports** — kills tree-shaking, use direct path exports
3. **Shared node_modules confusion** — always use `pnpm` for strict hoisting
4. **Not caching** — Turborepo's cache (local + remote) is the main benefit; configure `outputs` correctly
5. **Too many packages** — start with 2-3 shared packages, split more only when needed

## See Also

- `hub/testing-strategies.md` — Testing across packages
- `hub/deployment-patterns.md` — Deploying monorepo apps
- `guides/setting-up-ci-cd.md` — CI/CD for monorepos
