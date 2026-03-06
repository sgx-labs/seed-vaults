---
title: "REST Maturity Model (Richardson)"
tags: [rest, maturity-model, richardson, hateoas, levels, reference, entity]
content_type: entity
domain: api-design
---

# REST Maturity Model (Richardson)

Leonard Richardson's model describes four levels of REST maturity. Most APIs are Level 2. Very few reach Level 3. That's fine.

## Level 0: The Swamp of POX

Single endpoint, single HTTP method, actions encoded in the body.

```
POST /api
{"action": "getUser", "userId": 123}

POST /api
{"action": "createOrder", "items": [...]}
```

This is RPC over HTTP. It ignores everything HTTP offers. Avoid this.

## Level 1: Resources

Different URLs for different things, but still using one HTTP method.

```
POST /users/123
{"action": "get"}

POST /orders
{"action": "create", "items": [...]}
```

Better -- resources have identity. But HTTP methods aren't used correctly.

## Level 2: HTTP Methods

Resources + correct HTTP methods. This is where most modern APIs live.

```
GET    /users/123
POST   /orders
PUT    /users/123
DELETE /orders/456
```

Correct status codes, proper use of methods, content negotiation. This is "good enough REST" for nearly all practical purposes.

## Level 3: Hypermedia (HATEOAS)

Responses include links that tell the client what it can do next.

```json
{
  "id": 123,
  "name": "Alice",
  "email": "alice@test.com",
  "_links": {
    "self": {"href": "/users/123"},
    "orders": {"href": "/users/123/orders"},
    "update": {"href": "/users/123", "method": "PUT"},
    "delete": {"href": "/users/123", "method": "DELETE"}
  }
}
```

**In theory**: Clients discover the API by following links. No hardcoded URLs. The API is self-documenting.

**In practice**: Almost nobody implements this. Clients still hardcode endpoints. The overhead isn't worth it for most APIs.

## Practical Recommendation

**Target Level 2.** Use proper resources, HTTP methods, and status codes. Add HATEOAS only if your clients genuinely benefit from dynamic link discovery (rare).

Don't feel bad about not implementing Level 3. GitHub, Stripe, and Twilio are all Level 2 and they're considered excellent APIs.

## See Also

- `hub/rest-fundamentals.md` -- REST design principles
- `entities/http-status-codes.md` -- Status codes for Level 2 compliance
