---
title: "API Gateway Patterns"
tags: [api-gateway, routing, aggregation, bff, load-balancing, microservices]
content_type: hub
domain: api-design
---

# API Gateway Patterns

## Overview

An API gateway sits between clients and your backend services. It handles cross-cutting concerns like routing, authentication, rate limiting, and request transformation so individual services don't have to. Essential for microservices architectures, optional (but still useful) for monoliths.

## Core Responsibilities

| Concern | What the Gateway Does |
|---------|----------------------|
| Routing | Directs requests to the right backend service |
| Authentication | Validates tokens/keys before requests reach services |
| Rate limiting | Enforces per-client limits at the edge |
| Load balancing | Distributes traffic across service instances |
| Request transformation | Converts between protocols, formats, versions |
| Response aggregation | Combines responses from multiple services |
| Caching | Caches responses at the edge |
| Monitoring | Collects metrics, logs, and traces |
| Circuit breaking | Stops sending traffic to failing services |

## Gateway Patterns

### Simple Reverse Proxy
Routes requests to backends based on URL path.

```
/api/users/*    -> user-service:8080
/api/orders/*   -> order-service:8081
/api/products/* -> product-service:8082
```

Simplest pattern. Good starting point. Add capabilities as needed.

### Backend for Frontend (BFF)
Separate gateway per client type. Each BFF is optimized for its client's needs.

```
Mobile app  -> mobile-bff  -> [user-service, order-service]
Web app     -> web-bff     -> [user-service, order-service, analytics]
Partner API -> partner-bff -> [order-service, product-service]
```

**When to use**: Different clients need significantly different data shapes, response sizes, or authentication flows. Mobile needs minimal payloads; web needs rich responses.

**When NOT to use**: All clients consume the same data in the same way. Multiple BFFs are multiple services to maintain.

### Aggregation Gateway
Combines multiple backend calls into a single client request.

```
Client: GET /api/dashboard

Gateway:
  parallel:
    GET user-service/me
    GET order-service/recent
    GET notification-service/unread

Response:
  { "user": {...}, "recent_orders": [...], "notifications": [...] }
```

Reduces client-side complexity and round trips. Be careful with latency -- one slow backend slows the entire aggregated response.

### Protocol Translation
Converts between protocols at the edge.

```
Client (HTTP/JSON) -> Gateway -> Backend (gRPC/protobuf)
Client (REST)      -> Gateway -> Backend (GraphQL)
```

Lets you use gRPC internally while exposing REST externally.

## When to Use a Gateway

**Use when**: You have multiple backend services, need centralized auth/rate-limiting, different clients need different API shapes, you want to decouple client-facing API from internal architecture.

**Skip when**: You have a monolith with a single API, your infrastructure is simple, adding a gateway would be premature complexity.

## Common Mistakes

- **Gateway becomes a monolith** -- keep it thin. Business logic belongs in services, not the gateway.
- **Single point of failure** -- deploy multiple gateway instances behind a load balancer.
- **Too much transformation** -- if the gateway is doing complex data manipulation, you might need a proper BFF service instead.
- **No timeout propagation** -- gateway should enforce timeouts and propagate deadlines to backends.

## Popular Gateways

| Gateway | Type | Best For |
|---------|------|----------|
| Kong | Open source | General purpose, plugin ecosystem |
| Envoy | Open source | Microservices, service mesh |
| AWS API Gateway | Managed | AWS-native applications |
| Traefik | Open source | Container-native, auto-discovery |
| NGINX | Open source | Simple reverse proxy, high performance |

## See Also

- `hub/rate-limiting.md` -- Rate limiting at the gateway level
- `hub/authentication.md` -- Centralized auth at the gateway
- `hub/grpc-fundamentals.md` -- Protocol translation via gateway
- `research/microservices-communication.md` -- Service communication patterns
- `research/circuit-breaker.md` -- Circuit breaking at the gateway
