---
title: "API Versioning Strategies"
tags: [versioning, breaking-changes, compatibility, evolution, url-versioning, header-versioning]
content_type: hub
domain: api-design
---

# API Versioning Strategies

## Overview

APIs evolve. Versioning lets you make changes without breaking existing clients. The three main approaches are URL path versioning, header versioning, and query parameter versioning. Each has real tradeoffs -- there is no universally "correct" choice.

## URL Path Versioning

The version is part of the URL.

```
GET /v1/users
GET /v2/users
```

**Pros**: Visible, easy to understand, easy to route, easy to cache, easy to document. Clients always know which version they're hitting.

**Cons**: Strictly speaking, different URLs imply different resources (violates REST). Requires maintaining multiple route trees. Harder to deprecate incrementally.

**Who uses it**: Stripe, GitHub, Twitter, Google Cloud. It's the most common approach for public APIs.

## Header Versioning

The version is sent in a custom header or the `Accept` header.

```
GET /users HTTP/1.1
Accept: application/vnd.myapi.v2+json

# or
GET /users HTTP/1.1
API-Version: 2
```

**Pros**: Clean URLs (resource identity stays stable), supports content negotiation, can version individual endpoints.

**Cons**: Hidden -- clients can forget to set the header. Harder to test in a browser. Caching becomes more complex (need `Vary` header). Documentation is less obvious.

**Who uses it**: GitHub (also supports URL versioning), Azure.

## Query Parameter Versioning

The version is a query parameter.

```
GET /users?version=2
```

**Pros**: Easy to implement, visible in URL, easy to test.

**Cons**: Query parameters are for filtering, not versioning (semantic mismatch). Optional parameters mean clients might accidentally use the wrong version. Caching keys must include the parameter.

**Who uses it**: Some Google APIs, Amazon. Less common for new APIs.

## Comparison

| Factor | URL Path | Header | Query Param |
|--------|----------|--------|------------|
| Visibility | High | Low | Medium |
| Cacheability | Easy | Needs Vary | Needs cache key |
| REST purity | Lower | Higher | Lower |
| Client simplicity | Highest | Medium | High |
| Routing simplicity | Easy | Medium | Easy |
| Adoption | Most common | Niche | Uncommon |

## Best Practices (Regardless of Strategy)

1. **Version from day one.** Adding versioning later is painful. Start with v1.
2. **Don't version for every change.** Only version for breaking changes.
3. **Define what "breaking" means**: removing fields, changing types, removing endpoints, changing error formats. Adding fields is NOT breaking.
4. **Support at least N-1.** When you release v3, keep v2 alive. Give clients migration time.
5. **Sunset with notice.** Deprecation headers, changelog entries, and email notices before shutting down old versions.
6. **Document migration paths.** "To migrate from v1 to v2, change X and Y."

## When NOT to Version

- Internal APIs you control both sides of -- just update everything together
- APIs behind an API gateway that can transform requests
- Very early-stage APIs with no external consumers yet (iterate freely)

## Research Notes

- `research/api-versioning-strategies.md` -- Detailed comparison with real-world examples
- `guides/handling-breaking-changes.md` -- How to manage breaking changes gracefully

## See Also

- `hub/rest-fundamentals.md` -- REST conventions that versioning should preserve
- `hub/error-handling.md` -- Error format should be consistent across versions
- `hub/api-gateway.md` -- Gateways can handle version routing
