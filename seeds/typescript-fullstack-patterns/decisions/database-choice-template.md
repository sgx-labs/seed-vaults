---
title: "Database Choice: PostgreSQL vs MySQL vs SQLite"
tags: [decision, database, postgresql, mysql, sqlite, comparison]
content_type: decision
domain: typescript-fullstack
---

# Database Choice: PostgreSQL vs MySQL vs SQLite

## Decision Template

Use this template when choosing a database for your fullstack TypeScript project.

## Criteria Matrix

| Factor | PostgreSQL | MySQL | SQLite |
|--------|-----------|-------|--------|
| **TypeScript ORMs** | Prisma, Drizzle | Prisma, Drizzle | Prisma, Drizzle |
| **JSON support** | Excellent (jsonb) | Good (JSON type) | Basic |
| **Full-text search** | Built-in (tsvector) | Built-in (FULLTEXT) | FTS5 extension |
| **Edge runtime** | Via Neon, Supabase | Via PlanetScale | Via Turso (LibSQL) |
| **Managed hosting** | Neon, Supabase, RDS | PlanetScale, RDS | Turso |
| **Free tiers** | Generous (Neon, Supabase) | PlanetScale removed free | Turso free tier |
| **Serverless** | Neon serverless driver | PlanetScale serverless | Turso native |
| **Extensions** | pgvector, PostGIS, pg_trgm | Limited | Limited |
| **Connection pooling** | PgBouncer, Supavisor | Built-in | N/A (embedded) |
| **ACID compliance** | Full | Full (InnoDB) | Full (WAL mode) |
| **Scaling** | Read replicas, partitioning | Read replicas | Single writer |

## Decision Framework

### Choose PostgreSQL if:
- You need advanced features (JSONB, full-text search, vector search)
- You want the broadest ORM and hosting support
- You need PostGIS for geospatial data
- You want pgvector for AI/embedding use cases
- You're building a production SaaS

### Choose MySQL if:
- Your team has MySQL experience
- You're using PlanetScale (though evaluate Neon as alternative)
- You need high write throughput with simple schemas
- You're working with existing MySQL infrastructure

### Choose SQLite if:
- You want zero-infrastructure local development
- You're building an embedded or single-user app
- You're building a CLI tool with local storage
- You want Turso for edge-distributed SQLite
- Read-heavy workload with infrequent writes

## Your Decision

```
## AD-002: Database Choice

**Status:** [Proposed/Accepted]
**Date:** YYYY-MM-DD

**Decision:** [Which database and why]

**Hosting:** [Self-managed / Neon / Supabase / PlanetScale / Turso / RDS]

**ORM:** [Prisma / Drizzle]

**Key factors:**
1. [Most important reason]
2. [Second reason]
3. [Third reason]

**Alternative considered:** [What you didn't choose and why]
```

## See Also

- `hub/database-patterns.md` — Production database patterns
- `research/prisma-vs-drizzle.md` — ORM comparison
- `research/database-scaling.md` — Scaling strategies
- `entities/prisma-schema-reference.md` — Prisma quick reference
