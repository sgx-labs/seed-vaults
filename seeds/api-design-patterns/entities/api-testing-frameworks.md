---
title: "API Testing Frameworks"
tags: [testing, frameworks, integration, contract, e2e, reference, entity]
content_type: entity
domain: api-design
---

# API Testing Frameworks

## Testing Levels for APIs

| Level | What It Tests | Speed | Confidence |
|-------|--------------|-------|------------|
| Unit | Individual functions, handlers | Fast | Low (no integration) |
| Integration | Handler + database + middleware | Medium | Medium |
| Contract | API shape matches spec | Fast | Medium (schema only) |
| End-to-end | Full request through live server | Slow | High |
| Load | Performance under traffic | Slow | High (for perf) |

## Integration Testing Tools

| Tool | Language | Notes |
|------|----------|-------|
| **supertest** | JavaScript | HTTP assertions for Express/Koa/Fastify |
| **httptest** | Go | Standard library, in-process HTTP testing |
| **pytest + httpx** | Python | Async-friendly HTTP testing |
| **REST Assured** | Java | Fluent API for Java HTTP testing |
| **Hurl** | CLI | Plain-text HTTP test files, CI-friendly |

## Contract Testing

Verify that API responses match a defined schema.

| Tool | Approach |
|------|----------|
| **Pact** | Consumer-driven contracts between services |
| **Schemathesis** | Property-based testing from OpenAPI spec |
| **Dredd** | Test API against OpenAPI/API Blueprint spec |
| **Spectral** | Lint OpenAPI specs (not runtime, but catches spec issues) |

## End-to-End Testing

| Tool | Notes |
|------|-------|
| **Postman/Newman** | Run Postman collections from CLI |
| **k6** | Load testing that doubles as functional testing |
| **Hurl** | Sequential HTTP requests with assertions |
| **Step CI** | API testing from YAML definitions |

## Testing Best Practices

1. **Test the contract, not the implementation** -- assert response shape, not internal logic
2. **Use fixtures, not production data** -- deterministic, repeatable tests
3. **Test error cases** -- 400, 401, 403, 404, 422, 429 -- not just happy paths
4. **Test auth at every endpoint** -- missing auth, wrong role, expired token
5. **Test pagination boundaries** -- empty results, last page, invalid cursors
6. **Test rate limiting** -- verify 429 responses and Retry-After headers
7. **Automate in CI** -- tests that don't run automatically don't count

## See Also

- `research/load-testing.md` -- Performance testing strategies
- `research/openapi-best-practices.md` -- Specs that enable contract testing
- `entities/api-design-tools.md` -- Broader tool ecosystem
