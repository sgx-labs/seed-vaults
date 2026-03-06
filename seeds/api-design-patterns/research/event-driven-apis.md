---
title: "Event-Driven APIs (SSE, WebSocket, Polling)"
tags: [sse, websocket, polling, real-time, events, streaming, server-sent-events]
content_type: research
domain: api-design
---

# Event-Driven APIs (SSE, WebSocket, Polling)

## The Core Idea

REST is request-response. But many features need real-time data: notifications, live dashboards, chat, collaborative editing, price tickers. Four approaches exist: polling, long polling, Server-Sent Events (SSE), and WebSocket.

## Short Polling

Client repeatedly asks the server for updates.

```
Client: GET /notifications?since=2025-01-15T10:00:00Z  (every 5 seconds)
Server: 200 OK []                                       (no new data)
Client: GET /notifications?since=2025-01-15T10:00:00Z  (5 seconds later)
Server: 200 OK [{"message": "new order"}]               (data!)
```

**Pros**: Simplest to implement, works everywhere, stateless.
**Cons**: Wasteful (most polls return nothing), latency equals poll interval, scales poorly with many clients.

**When to use**: Low-frequency updates, when simplicity matters more than latency, when other approaches aren't available.

## Long Polling

Client sends a request, server holds it open until data is available or timeout.

```
Client: GET /notifications?since=X (request hangs)
... 30 seconds pass ...
Server: 200 OK [{"message": "new order"}]
Client: GET /notifications?since=Y (immediately reconnects)
```

**Pros**: Lower latency than short polling, works through most firewalls/proxies, simple client.
**Cons**: Server holds connections open (resource usage), timeout handling, reconnection logic.

**When to use**: When WebSocket/SSE aren't available, legacy clients, moderate update frequency.

## Server-Sent Events (SSE)

Unidirectional stream from server to client over HTTP. The server pushes events; the client receives them.

```
Client: GET /events
Accept: text/event-stream

Server: HTTP/1.1 200 OK
Content-Type: text/event-stream

data: {"type": "order.created", "order_id": "123"}

data: {"type": "payment.received", "amount": 5000}

event: notification
data: {"message": "Your order shipped"}
```

**Pros**: Simple (plain HTTP), automatic reconnection (built into EventSource API), works through most proxies, one-directional is often all you need.
**Cons**: Unidirectional (server to client only), limited browser connections per domain (6 in HTTP/1.1), text-only (no binary).

**When to use**: Notifications, live feeds, dashboards, any case where the server pushes and client just listens.

## WebSocket

Full-duplex, bidirectional communication over a persistent connection.

```
Client: GET /ws (upgrade request)
Server: 101 Switching Protocols

Client: {"type": "subscribe", "channel": "orders"}
Server: {"type": "order.created", "data": {...}}
Client: {"type": "message", "text": "hello"}
Server: {"type": "message", "text": "world"}
```

**Pros**: Bidirectional, low latency, binary support, efficient for high-frequency updates.
**Cons**: Stateful connections (harder to scale), not HTTP (different infrastructure), no automatic reconnection, some proxies/firewalls block WebSocket.

**When to use**: Chat, real-time collaboration, gaming, any scenario requiring bidirectional communication.

## Comparison

| Factor | Short Poll | Long Poll | SSE | WebSocket |
|--------|-----------|-----------|-----|-----------|
| Direction | Client->Server | Client->Server | Server->Client | Bidirectional |
| Latency | High (poll interval) | Medium | Low | Lowest |
| Complexity | Lowest | Low | Low | Medium |
| Scalability | Poor | Medium | Good | Good (stateful) |
| Browser support | Universal | Universal | Good* | Good |
| Proxy/firewall | No issues | Mostly fine | Mostly fine | Sometimes blocked |
| Reconnection | N/A | Manual | Automatic | Manual |

*SSE not supported in IE, but that's rarely relevant today.

## Decision Guide

1. **Do you need bidirectional communication?** -> WebSocket
2. **Is it server-to-client only?** -> SSE (simpler than WebSocket)
3. **Is the update frequency low (minutes)?** -> Short polling (simplest)
4. **Is WebSocket blocked by infrastructure?** -> Long polling or SSE
5. **Are you building a chat or collaborative editor?** -> WebSocket
6. **Are you building a notification feed or dashboard?** -> SSE

## Scaling Considerations

- **Polling**: Scales with request infrastructure. No persistent connections.
- **SSE**: Each client holds one HTTP connection. Use HTTP/2 to multiplex.
- **WebSocket**: Each client holds one TCP connection. Need sticky sessions or a pub/sub layer (Redis, NATS) to broadcast across server instances.

## See Also

- `hub/webhooks.md` -- Server-to-server event delivery
- `hub/rest-fundamentals.md` -- When REST request-response is sufficient
- `hub/graphql-fundamentals.md` -- GraphQL subscriptions as an alternative
