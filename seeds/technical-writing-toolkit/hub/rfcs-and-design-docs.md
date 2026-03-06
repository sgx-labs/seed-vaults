---
title: "RFCs and Design Docs"
tags: [rfc, design-doc, proposal, review, architecture, documentation]
content_type: hub
domain: technical-writing
---

# RFCs and Design Docs

## What They Are

An RFC (Request for Comments) is a proposal for a significant change. It describes the problem, proposes a solution, considers alternatives, and invites feedback before implementation begins. Design docs serve the same purpose with a less formal name.

The key difference from ADRs: RFCs are written *before* the decision. ADRs record *after*.

## When to Write an RFC

Write an RFC when:
- The change affects multiple teams or systems
- Implementation will take more than a few days
- The approach isn't obvious and reasonable people might disagree
- You're introducing a new technology, pattern, or dependency
- The change is hard to reverse

Skip the RFC when:
- The change is small and contained
- There's an obvious best approach that nobody would dispute
- You're fixing a bug

## The Template

```markdown
# RFC: Add real-time notifications via WebSocket

**Author:** Alice Chen
**Date:** 2025-03-01
**Status:** Under Review
**Reviewers:** Bob, Carol, Dave

## Problem

Users currently poll the /notifications endpoint every 30 seconds.
This creates unnecessary load (40% of API traffic) and delays
notification delivery by up to 30 seconds.

## Proposal

Add a WebSocket endpoint at /ws/notifications that pushes events
in real-time. Keep the REST endpoint for clients that can't use
WebSockets.

## Design

[Enough detail for reviewers to evaluate the approach. Diagrams
help. Include API shapes, data flow, and deployment considerations.]

## Alternatives Considered

**Server-Sent Events (SSE):** Simpler than WebSocket but one-directional.
Sufficient for notifications but limits future real-time features.
Chose WebSocket for extensibility.

**Long polling:** Less infrastructure change but still wasteful on
connections. Doesn't solve the latency problem.

## Risks and Mitigations

- **WebSocket connections consume memory.** Mitigation: connection
  limits per user, idle timeout at 5 minutes.
- **Load balancers need sticky sessions.** Mitigation: use Redis
  pub/sub for cross-instance delivery.

## Rollout Plan

1. Deploy WebSocket endpoint behind feature flag
2. Migrate internal dashboard first (1 week)
3. Open to API consumers with opt-in (2 weeks)
4. Deprecate polling endpoint after 90 days

## Open Questions

- Should we support binary messages or JSON only?
- What's the reconnection strategy for dropped connections?
```

## The Review Process

1. **Author writes the RFC** and shares with 2-4 reviewers
2. **Reviewers comment** within a defined window (typically 1 week)
3. **Author addresses comments** -- updates the RFC or responds with rationale
4. **Decision is made** -- accepted, rejected, or needs revision
5. **RFC is archived** -- status updated, linked from the decision log

## Where to Store RFCs

In the repo, alongside ADRs:

```
docs/
  decisions/          # ADRs (post-decision)
  rfcs/               # RFCs (pre-decision)
    001-websocket-notifications.md
    002-migrate-to-postgres.md
```

Or in a shared doc tool if your team prefers collaborative editing. The important thing is that they're findable and permanent.

## Common RFC Mistakes

- **Too long.** If it takes more than 15 minutes to read, it's too long. Split or summarize.
- **Solution without problem.** Always start with the problem. If the problem isn't clear, the solution doesn't matter.
- **No alternatives.** If you didn't consider alternatives, you didn't do enough thinking.
- **Missing rollout plan.** How will this get deployed? What's the rollback strategy?
- **No deadline for review.** Without a deadline, RFCs languish. Set a review window.

## See Also

- `hub/architecture-decision-records.md` -- For recording decisions after they're made
- `hub/writing-style-guide.md` -- Writing clearly under ambiguity
- `entities/common-documentation-templates.md` -- RFC template
- `guides/writing-your-first-adr.md` -- From RFC to ADR after the decision
