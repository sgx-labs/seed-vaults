---
title: "Microservices Communication Patterns"
tags: [microservices, synchronous, asynchronous, messaging, service-mesh, choreography, orchestration]
content_type: research
domain: api-design
---

# Microservices Communication Patterns

## The Core Idea

Microservices need to talk to each other. The choice between synchronous (HTTP/gRPC) and asynchronous (message queues/events) communication fundamentally shapes your system's reliability, latency, and complexity.

## Synchronous Communication

Service A calls Service B and waits for a response.

### HTTP/REST
```
Order Service -> GET user-service/users/123 -> wait for response -> continue
```

**Pros**: Simple, well-understood, easy to debug.
**Cons**: Tight coupling, cascading failures (if user-service is down, order-service fails), latency compounds with each hop.

### gRPC
```
Order Service -> UserService.GetUser(id: "123") -> wait for response -> continue
```

**Pros**: Faster than REST (binary protocol, HTTP/2), type-safe, streaming.
**Cons**: Same coupling and failure propagation as REST, plus protobuf complexity.

### When to Use Synchronous
- Request needs an immediate response (user-facing operations)
- Simple service-to-service calls with low latency requirements
- Queries that aggregate data from multiple services

## Asynchronous Communication

Service A sends a message and doesn't wait for a response.

### Message Queue (Point-to-Point)
```
Order Service -> [order.created message] -> Queue -> Payment Service
```

One producer, one consumer. The message is processed once.

**Tools**: RabbitMQ, Amazon SQS, Redis Streams.

### Event Bus (Pub/Sub)
```
Order Service -> publishes "order.created" event
  -> Payment Service (subscribes)
  -> Inventory Service (subscribes)
  -> Notification Service (subscribes)
```

One producer, many consumers. Each subscriber gets a copy.

**Tools**: Apache Kafka, Amazon SNS, NATS, Redis Pub/Sub.

### When to Use Asynchronous
- Fire-and-forget operations (send email, log event)
- Multiple services need to react to the same event
- Operations that can tolerate eventual consistency
- Protecting against cascading failures

## Orchestration vs Choreography

### Orchestration
A central service controls the flow.

```
Order Orchestrator:
  1. Call Payment Service -> charge card
  2. Call Inventory Service -> reserve items
  3. Call Shipping Service -> create shipment
  4. Call Notification Service -> send confirmation

If step 2 fails -> call Payment Service -> refund
```

**Pros**: Clear flow, easy to understand, centralized error handling.
**Cons**: Single point of failure, tight coupling to the orchestrator, bottleneck.

### Choreography
Each service reacts to events independently.

```
Order Service: publishes "order.created"
Payment Service: listens -> charges card -> publishes "payment.completed"
Inventory Service: listens -> reserves items -> publishes "items.reserved"
Shipping Service: listens for both -> creates shipment
```

**Pros**: Loosely coupled, no single point of failure, services are independent.
**Cons**: Hard to track the overall flow, distributed debugging, eventual consistency complexity.

### When to Use Which

| Scenario | Pattern | Why |
|----------|---------|-----|
| Linear workflow (A then B then C) | Orchestration | Clear sequence, easy error handling |
| Multiple independent reactions | Choreography | Services don't need to know about each other |
| Complex with compensation (saga) | Orchestration | Rollback logic is centralized |
| High independence between services | Choreography | Minimize coupling |

## Communication Patterns Summary

| Pattern | Coupling | Reliability | Complexity | Latency |
|---------|----------|-------------|------------|---------|
| Sync HTTP | High | Low (cascading failures) | Low | Direct |
| Sync gRPC | High | Low (cascading failures) | Medium | Direct |
| Async Queue | Low | High (buffered) | Medium | Delayed |
| Async Pub/Sub | Lowest | High (buffered) | High | Delayed |

## Common Mistakes

- **Everything synchronous** -- creates a fragile chain. One service down = everything down.
- **Everything asynchronous** -- adds complexity where a simple HTTP call would suffice.
- **No circuit breakers on sync calls** -- cascading failures are inevitable without them.
- **No dead letter queues** -- failed messages disappear silently.
- **No idempotency on consumers** -- messages get delivered more than once. Consumers must handle duplicates.

## See Also

- `hub/grpc-fundamentals.md` -- gRPC for synchronous communication
- `research/circuit-breaker.md` -- Protecting against cascading failures
- `hub/api-gateway.md` -- Gateway patterns for microservices
- `research/database-transaction-patterns.md` -- Data consistency across services
