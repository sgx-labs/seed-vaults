---
title: "Database MCP Servers — Query Your Data from Claude Code"
tags: [mcp, database, postgresql, sqlite, supabase, sql, read-only, schema]
content_type: research
domain: engineering
---

# Database MCP Servers — Query Your Data from Claude Code

## The Question

How do you let Claude Code query your databases directly? Database MCP servers expose SQL tools so Claude can explore schemas, run queries, and help with migrations — all without leaving the conversation.

## Available Database Servers

| Server | Package | Database | Status |
|--------|---------|----------|--------|
| PostgreSQL | `@modelcontextprotocol/server-postgres` | PostgreSQL | Official (Anthropic) |
| SQLite | `@modelcontextprotocol/server-sqlite` | SQLite | Official (Anthropic) |
| Supabase | `@supabase/mcp-server-supabase` | Supabase (Postgres) | First-party (Supabase) |
| MySQL | Community packages | MySQL/MariaDB | Community |

## PostgreSQL Setup

### 1. Install and Configure

```json
{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-postgres",
        "postgresql://readonly_user:password@localhost:5432/mydb"
      ],
      "type": "stdio"
    }
  }
}
```

### 2. Create a Read-Only User (Do This First)

```sql
-- Create a read-only role
CREATE ROLE claude_readonly WITH LOGIN PASSWORD 'a-strong-password';

-- Grant read access to existing tables
GRANT CONNECT ON DATABASE mydb TO claude_readonly;
GRANT USAGE ON SCHEMA public TO claude_readonly;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO claude_readonly;

-- Auto-grant read access to future tables
ALTER DEFAULT PRIVILEGES IN SCHEMA public
  GRANT SELECT ON TABLES TO claude_readonly;
```

### 3. What Tools It Provides

- **query** — Execute read-only SQL queries
- **list_tables** — Show all tables in the database
- **describe_table** — Show columns, types, and constraints for a table

## SQLite Setup

```json
{
  "mcpServers": {
    "sqlite": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-sqlite",
        "/path/to/database.db"
      ],
      "type": "stdio"
    }
  }
}
```

The SQLite server provides:
- **read_query** — Execute SELECT queries
- **write_query** — Execute INSERT/UPDATE/DELETE (use with caution)
- **create_table** — Create new tables
- **list_tables** — Show all tables
- **describe_table** — Show table schema
- **append_insight** — Store analysis notes

Note: Unlike the PostgreSQL server, the SQLite server allows writes by default. If you only want read access, point it at a copy of your database or use filesystem permissions.

## Supabase Setup

```json
{
  "mcpServers": {
    "supabase": {
      "command": "npx",
      "args": ["-y", "@supabase/mcp-server-supabase"],
      "type": "stdio",
      "env": {
        "SUPABASE_ACCESS_TOKEN": "your-access-token"
      }
    }
  }
}
```

Supabase MCP provides broader access beyond SQL: table management, edge functions, storage, and project configuration.

## Use Cases

### Explore an Unfamiliar Schema

```
You: "I just joined this project. Explore the database schema and explain how the data model works."
```

Claude will call `list_tables`, then `describe_table` on each, and produce a summary of entities and relationships.

### Debug Data Issues

```
You: "Users are reporting they can't see their recent orders. Check if orders for user_id 42 exist and have the correct status."
```

Claude runs the query and reports findings, including any anomalies.

### Generate Migrations

```
You: "I need to add a 'cancelled_at' timestamp to the orders table. Generate the migration SQL."
```

Claude checks the current schema first, then writes migration SQL that matches your existing patterns.

### Validate Business Logic

```
You: "Are there any orders with a total that doesn't match the sum of their line items?"
```

Claude writes and runs a validation query, flagging any mismatches.

## Security Rules

### Always Use Read-Only Connections

This is the most important rule. Claude is helpful but can make mistakes. A write-capable connection to production means a hallucinated `UPDATE` or `DELETE` could destroy data.

**Minimum viable setup:**
1. Create a read-only database user
2. Use that user's credentials in the MCP config
3. Verify: try an `INSERT` through the MCP tool to confirm it fails

### Never Expose Production Credentials

- Do not put connection strings in `.claude/settings.json` if it is checked into git
- Use environment variables or a local `.env` file (gitignored)
- Better: use `~/.claude/settings.json` (user-level, never committed) for database connections

```json
// In ~/.claude/settings.json (not in your repo)
{
  "mcpServers": {
    "postgres-dev": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "type": "stdio",
      "env": {
        "DATABASE_URL": "postgresql://readonly:pass@localhost:5432/devdb"
      }
    }
  }
}
```

### Scope Access Appropriately

| Environment | Access Level | Notes |
|-------------|-------------|-------|
| Local dev DB | Read-write OK | Your machine, your data |
| Shared dev/staging DB | Read-only | Other people's work is at stake |
| Production DB | Read-only, restricted | Only tables Claude needs to see |
| Production DB with PII | Do not connect | Use anonymized replicas instead |

## Practical Tips

- **Start with local/dev databases.** Get comfortable before touching staging or production.
- **Use `describe_table` before querying.** Claude makes better queries when it knows the schema.
- **Copy useful queries to your codebase.** Save them as migrations, reports, or ORM methods.
- **Watch for large result sets.** Ask Claude to use `LIMIT` clauses. Unbounded `SELECT *` floods the context window.
- **VPN/SSH tunnels**: Set up before starting Claude Code. MCP does not handle connection tunneling.
