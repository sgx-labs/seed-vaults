---
title: "Multi-Tenancy Patterns"
tags: [multi-tenancy, saas, isolation, tenant, shared-database, schema-per-tenant]
content_type: research
domain: api-design
---

# Multi-Tenancy Patterns

## The Core Idea

Multi-tenancy means a single application serves multiple customers (tenants) while keeping their data isolated. The API must route requests to the correct tenant, enforce data boundaries, and prevent cross-tenant data leaks. Three main strategies exist: shared database, schema-per-tenant, and database-per-tenant.

## Tenant Identification

How does the API know which tenant a request belongs to?

### Subdomain
```
https://acme.api.example.com/users
https://globex.api.example.com/users
```

**Pros**: Clean separation, easy to route, tenant visible in URL.
**Cons**: DNS/TLS configuration per tenant, wildcard certs needed.

### Header
```
GET /users HTTP/1.1
X-Tenant-ID: acme
```

**Pros**: Simple, no DNS changes, works with a single domain.
**Cons**: Easy to forget, requires middleware to enforce.

### Path Prefix
```
GET /tenants/acme/users
```

**Pros**: Visible, simple routing.
**Cons**: Pollutes URL structure, harder to switch tenants.

### From Auth Token
```
JWT claims: {"tenant_id": "acme", "user_id": "123", "role": "admin"}
```

**Pros**: Automatic from authentication, can't be forged, no extra parameter.
**Cons**: Tenant is implicit, harder to debug.

**Recommendation**: Derive tenant from auth token for most APIs. Use subdomain for customer-facing APIs where each tenant has a branded experience.

## Data Isolation Strategies

### Strategy 1: Shared Database, Shared Schema

All tenants share the same tables. A `tenant_id` column isolates data.

```sql
CREATE TABLE users (
  id UUID PRIMARY KEY,
  tenant_id UUID NOT NULL,
  name TEXT NOT NULL,
  email TEXT NOT NULL,
  UNIQUE(tenant_id, email)
);

-- Every query MUST include tenant_id
SELECT * FROM users WHERE tenant_id = 'acme' AND id = '123';
```

**Pros**: Simple, low operational cost, scales to thousands of tenants.
**Cons**: Risk of cross-tenant data leak if you forget the WHERE clause, shared performance (noisy neighbor), harder to backup/restore per tenant.

**Mitigation**: Use Row-Level Security (RLS) in PostgreSQL:
```sql
CREATE POLICY tenant_isolation ON users
  USING (tenant_id = current_setting('app.current_tenant')::uuid);
```

### Strategy 2: Schema-Per-Tenant

Each tenant gets their own database schema within a shared database.

```sql
-- Tenant: acme
CREATE SCHEMA acme;
CREATE TABLE acme.users (...);

-- Tenant: globex
CREATE SCHEMA globex;
CREATE TABLE globex.users (...);

-- Set schema per request
SET search_path TO acme;
SELECT * FROM users WHERE id = '123';
```

**Pros**: Strong isolation, easy per-tenant backup, no risk of forgetting WHERE clause.
**Cons**: Schema migrations run per tenant (slow at scale), connection pool per schema, limits at ~100-500 tenants.

### Strategy 3: Database-Per-Tenant

Each tenant gets their own database instance.

**Pros**: Strongest isolation, independent scaling, easy compliance (data residency), per-tenant backup and restore.
**Cons**: Highest operational cost, connection management complexity, limits at ~10-50 tenants without automation.

## Choosing a Strategy

| Factor | Shared DB | Schema-Per-Tenant | DB-Per-Tenant |
|--------|-----------|-------------------|---------------|
| Tenant count | 1000s+ | 100-500 | 10-50 |
| Isolation | Low (row-level) | Medium (schema) | High (full) |
| Operational cost | Low | Medium | High |
| Performance isolation | Low | Medium | High |
| Compliance flexibility | Low | Medium | High |
| Migration complexity | Low | Medium (per-schema) | High (per-DB) |

## API Design Considerations

- **Never expose tenant_id in URLs** if it's derived from auth tokens
- **Rate limit per tenant** -- prevent one tenant from exhausting shared resources
- **Separate admin API from tenant API** -- admin endpoints manage tenants, tenant endpoints manage data
- **Audit cross-tenant access** -- log any request that could access multiple tenants
- **Test with multiple tenants** -- your test suite should verify isolation

## See Also

- `hub/authorization.md` -- Tenant-aware authorization
- `hub/rate-limiting.md` -- Per-tenant rate limiting
- `research/api-security-checklist.md` -- Tenant isolation as security
