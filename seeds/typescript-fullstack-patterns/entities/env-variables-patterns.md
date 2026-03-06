---
title: "Environment Variables Patterns"
tags: [env, environment-variables, dotenv, validation, type-safe, t3-env, reference, entity]
content_type: entity
domain: typescript-fullstack
---

# Environment Variables Patterns

## Next.js Env Loading Order

1. `process.env` (system/container env)
2. `.env.$(NODE_ENV).local` — e.g., `.env.development.local`
3. `.env.local` — NOT loaded in test environment
4. `.env.$(NODE_ENV)` — e.g., `.env.development`
5. `.env`

Later files do NOT override earlier ones. System env always wins.

## Client vs Server

| Prefix | Accessible in | Bundle included |
|--------|--------------|-----------------|
| `NEXT_PUBLIC_` | Server + Client | Yes (public) |
| None | Server only | No |

```typescript
// Server only — safe for secrets
DATABASE_URL="postgresql://..."
AUTH_SECRET="super-secret-key"
STRIPE_SECRET_KEY="sk_live_..."

// Client accessible — NEVER put secrets here
NEXT_PUBLIC_APP_URL="https://myapp.com"
NEXT_PUBLIC_POSTHOG_KEY="phc_..."
```

**Footgun**: `NEXT_PUBLIC_` variables are embedded in the JavaScript bundle at build time. Anyone can read them. Never use this prefix for API keys, database URLs, or secrets.

## Type-Safe Validation with t3-env

```typescript
// env.ts
import { createEnv } from "@t3-oss/env-nextjs";
import { z } from "zod";

export const env = createEnv({
  server: {
    DATABASE_URL: z.string().url(),
    AUTH_SECRET: z.string().min(32),
    STRIPE_SECRET_KEY: z.string().startsWith("sk_"),
    RESEND_API_KEY: z.string().startsWith("re_"),
    NODE_ENV: z.enum(["development", "production", "test"]).default("development"),
  },
  client: {
    NEXT_PUBLIC_APP_URL: z.string().url(),
    NEXT_PUBLIC_POSTHOG_KEY: z.string().optional(),
  },
  runtimeEnv: {
    DATABASE_URL: process.env.DATABASE_URL,
    AUTH_SECRET: process.env.AUTH_SECRET,
    STRIPE_SECRET_KEY: process.env.STRIPE_SECRET_KEY,
    RESEND_API_KEY: process.env.RESEND_API_KEY,
    NODE_ENV: process.env.NODE_ENV,
    NEXT_PUBLIC_APP_URL: process.env.NEXT_PUBLIC_APP_URL,
    NEXT_PUBLIC_POSTHOG_KEY: process.env.NEXT_PUBLIC_POSTHOG_KEY,
  },
});

// Usage — fully typed, validated at build time
import { env } from "@/env";
const dbUrl = env.DATABASE_URL; // string (guaranteed)
```

## .env.example Template

```bash
# .env.example — commit this to git
# Copy to .env.local and fill in values

# Database
DATABASE_URL="postgresql://user:password@localhost:5432/mydb"

# Auth
AUTH_SECRET="" # openssl rand -base64 32
AUTH_GITHUB_ID=""
AUTH_GITHUB_SECRET=""

# Stripe
STRIPE_SECRET_KEY=""
STRIPE_WEBHOOK_SECRET=""

# Email
RESEND_API_KEY=""

# Public
NEXT_PUBLIC_APP_URL="http://localhost:3000"
```

## .gitignore Rules

```gitignore
# Environment files
.env
.env.local
.env.*.local

# Keep .env.example
!.env.example
```

## See Also

- `hub/deployment-patterns.md` — Environment variables per platform
- `research/security-checklist.md` — Secrets management
