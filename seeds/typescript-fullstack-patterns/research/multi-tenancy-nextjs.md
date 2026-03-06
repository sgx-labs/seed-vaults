---
title: "Implementing Multi-Tenancy in Next.js"
tags: [multi-tenancy, tenant, subdomain, database, row-level-security, nextjs, saas]
content_type: research
domain: typescript-fullstack
---

# Implementing Multi-Tenancy in Next.js

## The Core Idea

Multi-tenancy lets a single app serve multiple organizations with isolated data. Three approaches: shared database with row-level filtering, shared database with schema-per-tenant, or separate databases per tenant. For most SaaS apps, shared database with row-level filtering is the right choice.

## Tenant Identification

### Subdomain-Based (Preferred for SaaS)

```typescript
// middleware.ts
import { NextResponse } from "next/server";

export function middleware(request: NextRequest) {
  const hostname = request.headers.get("host") ?? "";
  const subdomain = hostname.split(".")[0];

  // Skip for main domain and special subdomains
  if (subdomain === "www" || subdomain === "app" || !subdomain.match(/^[a-z0-9-]+$/)) {
    return NextResponse.next();
  }

  // Pass tenant to the app via headers
  const response = NextResponse.next();
  response.headers.set("x-tenant-slug", subdomain);
  return response;
}
```

```typescript
// lib/tenant.ts
import { headers } from "next/headers";
import { cache } from "react";

export const getCurrentTenant = cache(async () => {
  const headerList = await headers();
  const slug = headerList.get("x-tenant-slug");
  if (!slug) return null;

  return db.tenant.findUnique({
    where: { slug },
    select: { id: true, slug: true, name: true, plan: true },
  });
});
```

### Path-Based

```
/org/acme/dashboard    -> tenant = "acme"
/org/globex/settings   -> tenant = "globex"
```

```typescript
// app/org/[tenantSlug]/layout.tsx
export default async function TenantLayout({
  params,
  children,
}: {
  params: Promise<{ tenantSlug: string }>;
  children: React.ReactNode;
}) {
  const { tenantSlug } = await params;
  const tenant = await db.tenant.findUnique({ where: { slug: tenantSlug } });

  if (!tenant) notFound();

  return (
    <TenantProvider tenant={tenant}>
      {children}
    </TenantProvider>
  );
}
```

## Data Isolation — Row-Level Filtering

Every table includes a `tenantId` column. Every query filters by it.

```prisma
model Project {
  id       String @id @default(cuid())
  name     String
  tenantId String
  tenant   Tenant @relation(fields: [tenantId], references: [id])

  @@index([tenantId])
}
```

**Prisma middleware** — automatic tenant scoping:

```typescript
function tenantMiddleware(tenantId: string) {
  return db.$extends({
    query: {
      $allModels: {
        async findMany({ args, query }) {
          args.where = { ...args.where, tenantId };
          return query(args);
        },
        async create({ args, query }) {
          args.data = { ...args.data, tenantId };
          return query(args);
        },
      },
    },
  });
}

// Usage
const tenantDb = tenantMiddleware(tenant.id);
const projects = await tenantDb.project.findMany(); // Auto-filtered by tenant
```

## PostgreSQL Row-Level Security (RLS)

For bulletproof isolation, enforce at the database level.

```sql
-- Enable RLS
ALTER TABLE projects ENABLE ROW LEVEL SECURITY;

-- Policy: users can only see their tenant's projects
CREATE POLICY tenant_isolation ON projects
  USING (tenant_id = current_setting('app.tenant_id')::text);

-- Set tenant context per request
SET app.tenant_id = 'tenant_abc123';
SELECT * FROM projects; -- Only returns tenant's projects
```

```typescript
// Set tenant context before queries
async function withTenant<T>(tenantId: string, fn: () => Promise<T>): Promise<T> {
  await db.$executeRawUnsafe(`SET app.tenant_id = '${tenantId}'`);
  return fn();
}
```

## Common Mistakes

1. **Forgetting tenant filter on one query** — leaks data between tenants. Use middleware or RLS to enforce.
2. **Not indexing `tenantId`** — every query filters by tenant, so this index is critical.
3. **Tenant slug in URL without validation** — always verify the user belongs to the requested tenant.
4. **Global caches without tenant scoping** — cache keys must include tenant ID.
5. **Not testing cross-tenant isolation** — write tests that verify Tenant A cannot see Tenant B's data.

## See Also

- `hub/database-patterns.md` — Database patterns
- `hub/authentication-patterns.md` — Auth with tenant context
- `research/database-scaling.md` — Scaling multi-tenant databases
