---
title: "Webhooks Design"
tags: [webhooks, callbacks, events, notifications, push, async]
content_type: hub
domain: api-design
---

# Webhooks Design

## Overview

Webhooks let your API push events to clients instead of clients polling for changes. When something happens (order placed, payment processed, user signed up), your server sends an HTTP POST to the client's registered URL. Simple concept, surprisingly tricky to get right.

## Basic Flow

```
1. Client registers a webhook URL: POST /webhooks
   {"url": "https://client.com/hooks", "events": ["order.created", "payment.completed"]}

2. Event occurs in your system

3. Your server sends:
   POST https://client.com/hooks
   {
     "id": "evt_abc123",
     "type": "order.created",
     "created_at": "2025-01-15T10:30:00Z",
     "data": {
       "order_id": "ord_456",
       "amount": 5000,
       "currency": "USD"
     }
   }

4. Client responds with 2xx to acknowledge receipt
```

## Webhook Payload Design

### Event Envelope
Every webhook should have a consistent envelope:

```json
{
  "id": "evt_abc123",
  "type": "order.created",
  "api_version": "2025-01-15",
  "created_at": "2025-01-15T10:30:00Z",
  "data": { ... }
}
```

- `id` -- unique event ID for deduplication
- `type` -- dot-separated event type
- `api_version` -- which version of the payload schema
- `created_at` -- when the event occurred
- `data` -- the actual payload

### Fat vs Thin Payloads

**Fat**: Include the full resource in the webhook payload.
```json
{"type": "order.created", "data": {"id": "ord_456", "amount": 5000, "items": [...]}}
```

**Thin**: Include only the ID. Client fetches details via API.
```json
{"type": "order.created", "data": {"order_id": "ord_456"}}
```

**Recommendation**: Fat payloads for most cases. Thin payloads if data is sensitive or very large. Stripe uses fat payloads with the option to fetch via API for verification.

## Security

### Signature Verification
Sign every webhook with a shared secret so clients can verify authenticity.

```
webhook_signature = HMAC-SHA256(shared_secret, request_body)

Headers:
  X-Webhook-Signature: sha256=abc123def456...
  X-Webhook-Timestamp: 1705312200
```

Clients verify by computing the HMAC and comparing. Include a timestamp to prevent replay attacks -- reject events older than 5 minutes.

### Client-Side Verification
Optionally, clients can fetch the event via your API to confirm it's real:
```
1. Receive webhook with event ID evt_abc123
2. GET /events/evt_abc123 to verify it exists
3. Process the verified event
```

## Delivery Guarantees

### Retry Strategy
If the client returns non-2xx or doesn't respond within the timeout:

```
Attempt 1: immediate
Attempt 2: 1 minute
Attempt 3: 5 minutes
Attempt 4: 30 minutes
Attempt 5: 2 hours
Attempt 6: 8 hours
Attempt 7: 24 hours
```

Exponential backoff with jitter. After max retries, mark the webhook as failed and notify the client.

### At-Least-Once Delivery
Webhooks should guarantee at-least-once delivery. This means clients MUST handle duplicate events. The event `id` field enables deduplication.

### Ordering
Don't guarantee ordering. Events may arrive out of order due to retries and concurrent processing. Include timestamps and let clients handle ordering if needed.

## Management API

Let clients manage their webhooks:

```
POST   /webhooks              # Register a new webhook
GET    /webhooks              # List registered webhooks
GET    /webhooks/:id          # Get webhook details
PATCH  /webhooks/:id          # Update URL or events
DELETE /webhooks/:id          # Remove a webhook
GET    /webhooks/:id/events   # View delivery history
POST   /webhooks/:id/test     # Send a test event
```

## Common Mistakes

- **No signature verification** -- anyone can forge webhook payloads
- **No retry logic** -- clients miss events when their server is temporarily down
- **Synchronous processing** -- your webhook sender blocks on slow clients. Use a queue.
- **No event log** -- clients have no way to see what was sent or replay missed events
- **No deduplication guidance** -- clients process the same event twice

## See Also

- `research/event-driven-apis.md` -- Webhooks vs SSE vs WebSocket vs polling
- `hub/authentication.md` -- Securing webhook endpoints
- `hub/error-handling.md` -- How to handle failed webhook deliveries
