---
title: "Database Choice for API Backend — Decision Template"
tags: [decision, database, postgresql, mongodb, redis, architecture, template]
content_type: decision
domain: api-design
---

# Database Choice for API Backend — Decision Template

Use this template when choosing a database for your API backend.

## Context

[Describe your API: data model, read/write ratio, scale expectations, consistency requirements.]

## Decision Criteria

| Factor | Your Situation |
|--------|---------------|
| Data model | [Relational / Document / Key-Value / Graph / Time-Series] |
| Consistency requirements | [Strong / Eventual / Doesn't matter] |
| Read/write ratio | [Read-heavy / Write-heavy / Balanced] |
| Expected data volume | [< 1GB / 1-100GB / 100GB-1TB / > 1TB] |
| Query complexity | [Simple lookups / Joins / Aggregations / Full-text search] |
| Team experience | [SQL / NoSQL / Both] |
| Hosting preference | [Self-managed / Managed service / Serverless] |

## Options Analysis

### Option A: PostgreSQL

```
Best if: Relational data, complex queries, ACID transactions, most API backends
Strengths: Mature, extensible (JSONB, full-text search), strong ecosystem
Weakness: Horizontal scaling requires more effort (though usually fine to 1TB+)
```

**When to choose**: Default choice for most APIs. Start here unless you have a specific reason not to. Handles JSON documents (JSONB), full-text search, and time-series data reasonably well -- you may not need a specialized database.

### Option B: MongoDB

```
Best if: Document-oriented data, schema flexibility, rapid iteration
Strengths: Flexible schema, horizontal scaling, document model
Weakness: No joins (lookups are limited), eventual consistency by default, transactions added late
```

**When to choose**: Your data is genuinely document-shaped (nested, variable structure), you need horizontal scaling from day one, or your team is significantly more productive with MongoDB.

### Option C: Redis

```
Best if: Caching, rate limiting, sessions, real-time leaderboards
Strengths: Extremely fast, versatile data structures, pub/sub
Weakness: In-memory (cost at scale), not a primary database for most use cases
```

**When to choose**: As a complement to your primary database. Rate limiting, session storage, caching layer, job queues. Rarely the only database.

### Option D: SQLite

```
Best if: Embedded, single-server, low-traffic APIs, development
Strengths: Zero configuration, serverless, fast for reads, single file
Weakness: Single writer, no network access, limited concurrent writes
```

**When to choose**: Single-server deployments, embedded APIs, prototyping, CLI tools with local data. Increasingly viable for production with tools like Litestream (replication) and Turso (distributed SQLite).

### Option E: DynamoDB / Cloud-Native

```
Best if: Serverless architecture, predictable access patterns, massive scale
Strengths: Fully managed, auto-scaling, single-digit ms latency at any scale
Weakness: Limited query flexibility, requires careful key design, vendor lock-in
```

**When to choose**: AWS-native architecture, serverless functions, access patterns are well-defined and unlikely to change.

## Decision

**Primary database:** [PostgreSQL / MongoDB / etc.]
**Supporting databases:** [Redis for caching / etc.]

**Rationale:**
[Why this combination fits your API.]

**Migration path:**
[How you'd move to a different database if this choice doesn't work out.]

## Practical Advice

If you're unsure: **start with PostgreSQL**. It handles 90% of use cases well. Add Redis for caching and rate limiting if needed. Specialize only when you hit actual limitations, not theoretical ones.

## See Also

- `research/database-transaction-patterns.md` -- Transaction patterns across databases
- `research/database-rate-limiting.md` -- Database-backed rate limiting
- `hub/caching.md` -- Caching strategies (often involves Redis)
