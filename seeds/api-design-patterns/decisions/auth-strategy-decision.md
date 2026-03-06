---
title: "Auth Strategy — Decision Template"
tags: [decision, authentication, authorization, architecture, template]
content_type: decision
domain: api-design
---

# Auth Strategy — Decision Template

Use this template when choosing an authentication approach for your API.

## Context

[Describe your API: who calls it (browsers, mobile, servers, third parties), what data it protects, compliance requirements.]

## Decision Criteria

| Factor | Your Situation |
|--------|---------------|
| Client types | [Browser / Mobile / Server / CLI / Third-party] |
| User identity needed? | [Yes / No] |
| Third-party delegation? | [Yes / No] |
| Session revocation speed | [Immediate / Within minutes / Eventually] |
| Compliance requirements | [None / SOC2 / HIPAA / PCI / GDPR] |
| Multi-service architecture? | [Monolith / Microservices] |
| Team auth experience | [None / Basic / Advanced] |

## Options Analysis

### Option A: API Keys

```
Best if: Server-to-server, developer platform, usage tracking
Risk: Keys can't represent user identity
Complexity: Low
```

- [ ] Fits your client types?
- [ ] Meets security requirements?
- [ ] Team can implement and maintain?

### Option B: JWT (Access Token + Refresh Token)

```
Best if: First-party apps, microservices, stateless APIs
Risk: Can't revoke immediately without blocklist
Complexity: Medium
```

- [ ] Fits your client types?
- [ ] Acceptable revocation latency?
- [ ] Team understands token lifecycle?

### Option C: OAuth2 (Authorization Code + PKCE)

```
Best if: Third-party access, "Sign in with X", delegated authorization
Risk: Complex to implement correctly
Complexity: High
```

- [ ] Third-party access needed?
- [ ] Team has OAuth2 experience (or budget for auth service)?
- [ ] Acceptable implementation timeline?

### Option D: Session Tokens (Server-Side Sessions)

```
Best if: Traditional web apps, same-domain, need instant revocation
Risk: Requires server-side state, doesn't scale horizontally without shared store
Complexity: Low
```

- [ ] Same-domain only?
- [ ] Acceptable session store dependency?
- [ ] No cross-domain or mobile requirements?

### Option E: Managed Auth Service (Auth0, Clerk, Firebase Auth)

```
Best if: Small team, need OAuth2/social login, don't want to build auth
Risk: Vendor dependency, cost at scale
Complexity: Low (for your team)
```

- [ ] Budget for auth service?
- [ ] Acceptable vendor dependency?
- [ ] Meets compliance requirements?

## Decision

**Chosen approach:** [API Keys / JWT / OAuth2 / Sessions / Managed / Combination]

**Rationale:**
[Why this choice fits your situation. Reference the criteria above.]

**Implementation plan:**
1. [First step]
2. [Second step]
3. [Third step]

**Tradeoffs accepted:**
[What you're giving up with this choice.]

## See Also

- `hub/authentication.md` -- Authentication patterns in detail
- `hub/authorization.md` -- Authorization (what comes after auth)
- `research/oauth2-flows.md` -- OAuth2 flow details
- `guides/adding-auth.md` -- Implementation guide
