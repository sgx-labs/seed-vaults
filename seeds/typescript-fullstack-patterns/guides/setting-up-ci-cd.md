---
title: "Setting Up CI/CD for Next.js (GitHub Actions)"
tags: [guide, ci-cd, github-actions, testing, deployment, automation]
content_type: guide
domain: typescript-fullstack
---

# Setting Up CI/CD for Next.js (GitHub Actions)

## Step 1: Create the Workflow

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  check:
    name: Lint, Type Check, Test
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: pnpm/action-setup@v4
        with:
          version: 9

      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: "pnpm"

      - run: pnpm install --frozen-lockfile

      - name: Lint
        run: pnpm lint

      - name: Type check
        run: pnpm typecheck

      - name: Unit + Integration tests
        run: pnpm test run

      - name: Build
        run: pnpm build
        env:
          DATABASE_URL: ${{ secrets.DATABASE_URL }}
          AUTH_SECRET: "ci-secret-not-real"
          NEXT_PUBLIC_APP_URL: "http://localhost:3000"

  e2e:
    name: E2E Tests
    runs-on: ubuntu-latest
    needs: check

    steps:
      - uses: actions/checkout@v4

      - uses: pnpm/action-setup@v4
        with:
          version: 9

      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: "pnpm"

      - run: pnpm install --frozen-lockfile
      - run: pnpm exec playwright install --with-deps chromium

      - name: Run E2E tests
        run: pnpm test:e2e
        env:
          DATABASE_URL: ${{ secrets.DATABASE_URL }}

      - uses: actions/upload-artifact@v4
        if: failure()
        with:
          name: playwright-report
          path: playwright-report/
```

## Step 2: Add Database for CI (Optional)

```yaml
# Add to the check job:
services:
  postgres:
    image: postgres:16
    env:
      POSTGRES_USER: test
      POSTGRES_PASSWORD: test
      POSTGRES_DB: testdb
    options: >-
      --health-cmd pg_isready
      --health-interval 10s
      --health-timeout 5s
      --health-retries 5
    ports:
      - 5432:5432

# Use in env:
env:
  DATABASE_URL: "postgresql://test:test@localhost:5432/testdb"
```

## Step 3: Cache Optimization

```yaml
# Cache Prisma Client
- name: Cache Prisma Client
  uses: actions/cache@v4
  with:
    path: node_modules/.prisma
    key: prisma-${{ hashFiles('prisma/schema.prisma') }}

# Cache Next.js build
- name: Cache Next.js
  uses: actions/cache@v4
  with:
    path: .next/cache
    key: nextjs-${{ hashFiles('pnpm-lock.yaml') }}-${{ hashFiles('**/*.ts', '**/*.tsx') }}
    restore-keys: nextjs-${{ hashFiles('pnpm-lock.yaml') }}-
```

## Step 4: Monorepo CI (Turborepo)

```yaml
- name: Build affected packages
  run: pnpm turbo build --filter="...[origin/main]"
  # Only builds packages affected by changes
```

## Step 5: Branch Protection

In GitHub repo settings:
1. Require status checks to pass before merging
2. Require branches to be up to date before merging
3. Select "check" job as required
4. Optionally require "e2e" job

## See Also

- `hub/testing-strategies.md` — Test strategy
- `hub/deployment-patterns.md` — Deploy targets
- `hub/monorepo-setup.md` — Monorepo CI patterns
