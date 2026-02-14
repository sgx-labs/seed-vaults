---
title: "Database Choices — Pick One and Move On"
tags: [building, database, infrastructure, decisions]
content_type: research
domain: business
---

# Database Choices — Pick One and Move On

## The Rule

Do not spend more than 30 minutes choosing a database. For 95% of indie projects, any of the top three options will work. You will not hit database limits until you have thousands of users. By then, you will have revenue to handle migration.

## The Quick Decision

| Your stack | Use this database | Why |
|-----------|------------------|-----|
| Next.js | Supabase (Postgres) | Auth + DB + storage in one |
| Rails | PostgreSQL | Built-in support, just works |
| Django | PostgreSQL | Same |
| Laravel | MySQL or SQLite | Laravel defaults |
| Any stack, simple needs | SQLite | Zero infrastructure |
| Any stack, want managed | PlanetScale or Neon | Serverless, scales automatically |

## Database Options Ranked by Simplicity

### 1. SQLite (simplest)
- **Pros:** Zero config, single file, fast, free
- **Cons:** Single-writer, no built-in cloud sync
- **Best for:** Solo founder apps, MVP stage, CLI tools
- **Note:** Pieter Levels runs multi-million-dollar products on SQLite

### 2. Supabase (best all-in-one)
- **Pros:** Postgres + Auth + Storage + Realtime. Free tier. Dashboard.
- **Cons:** Vendor lock-in for auth/storage features
- **Best for:** Next.js apps, rapid prototyping, apps needing auth
- **Cost:** Free for 2 projects, $25/mo for production

### 3. PostgreSQL (most capable)
- **Pros:** Industry standard, handles everything, great tooling
- **Cons:** Need to host it somewhere
- **Best for:** Any project that may grow
- **Host on:** Railway ($5/mo), Render, Neon (serverless)

### 4. PlanetScale / Neon (serverless)
- **Pros:** Auto-scaling, branching, generous free tiers
- **Cons:** PlanetScale removed free tier (moved to Vitess licensing changes)
- **Best for:** Serverless architectures
- **Note:** Neon has a generous free tier and is Postgres-compatible

## What NOT to Choose for an MVP

| Technology | Why not |
|-----------|---------|
| MongoDB | Rarely needed for indie SaaS. SQL is simpler for most use cases. |
| DynamoDB | AWS complexity tax. Fine for large scale, overkill for MVP. |
| Redis (as primary) | Great for caching, not for primary data storage. |
| Custom graph DB | Unless your product IS a graph, this is over-engineering. |
| Multi-database setup | One database. Period. Until you absolutely must split. |

## Schema Design for Speed

Keep it simple:
1. Users table (id, email, created_at)
2. One table per core entity (projects, documents, tasks, etc.)
3. Foreign keys to users table
4. Timestamps on everything (created_at, updated_at)
5. Soft deletes if needed (deleted_at)

Do NOT build:
- Audit logging tables (until needed for compliance)
- Activity stream tables (until you build that feature)
- Complex permission tables (start with is_admin boolean)
- Multi-tenant schemas (start with a simple user_id foreign key)

## The Migration Question

"But what if I need to migrate later?" You probably will not. And if you do, it is a good problem to have — it means you have users and revenue to justify the effort.
