---
title: "Database Transaction Patterns for APIs"
tags: [database, transactions, consistency, saga, two-phase-commit, optimistic-locking]
content_type: research
domain: api-design
---

# Database Transaction Patterns for APIs

## The Core Idea

APIs that modify data need to handle concurrent access, partial failures, and consistency. Database transactions are the foundation, but distributed systems (microservices) can't use a single database transaction across services. Different patterns handle different consistency requirements.

## Single-Service Patterns

### Database Transactions

Wrap related operations in a single transaction.

```
BEGIN TRANSACTION
  INSERT INTO orders (id, user_id, total) VALUES (...)
  UPDATE inventory SET quantity = quantity - 1 WHERE product_id = ...
  INSERT INTO order_items (order_id, product_id) VALUES (...)
COMMIT
```

If any step fails, everything rolls back. Simple and reliable within a single database.

### Optimistic Locking

Don't lock rows. Instead, check that data hasn't changed before writing.

```
1. Read:  SELECT * FROM products WHERE id = 123  (version = 5)
2. Modify in application
3. Write: UPDATE products SET ..., version = 6 WHERE id = 123 AND version = 5
4. If 0 rows affected -> someone else modified it -> retry or return 409 Conflict
```

**When to use**: Low contention, read-heavy workloads, web forms where users edit and save.

**When NOT to use**: High contention on the same rows, operations that can't be easily retried.

### Pessimistic Locking

Lock the row when you read it. Others wait.

```sql
SELECT * FROM accounts WHERE id = 123 FOR UPDATE
-- Row is locked until transaction commits
UPDATE accounts SET balance = balance - 100 WHERE id = 123
COMMIT  -- Lock released
```

**When to use**: High contention, financial operations, inventory management where double-spending is unacceptable.

**When NOT to use**: Read-heavy workloads (locks cause contention), long-running operations.

## Multi-Service Patterns

### Saga Pattern

A sequence of local transactions. Each service commits its own transaction. If a step fails, compensation transactions undo previous steps.

```
Order Saga:
  1. Order Service: create order (status: pending)
  2. Payment Service: charge card
  3. Inventory Service: reserve items
  4. Order Service: update order (status: confirmed)

Compensation (if step 3 fails):
  2c. Payment Service: refund charge
  1c. Order Service: cancel order
```

**Orchestrated saga**: A central coordinator manages the steps.
**Choreographed saga**: Each service publishes events, next service reacts.

**Pros**: Works across services, no distributed locks.
**Cons**: Complex, eventual consistency, compensation logic can be tricky.

### Outbox Pattern

Ensure that database changes and event publishing happen atomically.

```
BEGIN TRANSACTION
  INSERT INTO orders (id, ...) VALUES (...)
  INSERT INTO outbox (event_type, payload) VALUES ('order.created', '{...}')
COMMIT

-- Background process polls outbox table, publishes events, marks as sent
```

Prevents the "database committed but event wasn't published" problem.

## API-Level Patterns

### Idempotent Writes

```
POST /orders
Idempotency-Key: abc-123

Server:
  1. Check if abc-123 already processed -> return cached response
  2. Process order in transaction
  3. Store result keyed by abc-123
  4. Return response
```

See `hub/idempotency.md` for details.

### Conflict Detection

Return 409 Conflict when concurrent modifications are detected.

```json
{
  "type": "https://api.example.com/errors/conflict",
  "title": "Conflict",
  "status": 409,
  "detail": "This resource was modified by another request. Current version: 7, your version: 5."
}
```

Include the current version so clients can merge or retry.

## Choosing a Pattern

| Scenario | Pattern | Why |
|----------|---------|-----|
| Single database, simple writes | Database transaction | Simplest, strongest guarantees |
| Single database, concurrent reads/writes | Optimistic locking | Low overhead, handles contention |
| Financial operations | Pessimistic locking | Prevents double-spending |
| Cross-service workflow | Saga | Only option for distributed consistency |
| Cross-service event publishing | Outbox pattern | Atomic DB + event guarantee |

## See Also

- `hub/idempotency.md` -- Idempotent API operations
- `hub/error-handling.md` -- 409 Conflict responses
- `research/microservices-communication.md` -- Service communication patterns
