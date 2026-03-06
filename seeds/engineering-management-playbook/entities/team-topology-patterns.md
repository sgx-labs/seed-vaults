---
title: "Team Topology Patterns"
tags: [team-topology, organization, stream-aligned, platform, enabling, complicated-subsystem]
content_type: entity
domain: engineering-management
---

# Team Topology Patterns

## The Four Team Types (from "Team Topologies" by Skelton & Pais)

### 1. Stream-Aligned Team
**Purpose**: Delivers value along a single stream of work (a product, a feature set, a user journey).

- Owns their full delivery pipeline: design, build, test, deploy, operate
- Long-lived, not project-based
- 5-9 members (two-pizza team)
- Primary team type -- most engineers should be on stream-aligned teams

**Example**: "Checkout Team" owns everything from cart to payment confirmation.

### 2. Platform Team
**Purpose**: Provides internal services that reduce cognitive load for stream-aligned teams.

- Treats stream-aligned teams as customers
- Provides self-service capabilities (CI/CD, monitoring, deployment, infrastructure)
- Success metric: stream-aligned teams ship faster without asking platform for help
- Should be small relative to the number of teams it supports

**Example**: "Developer Platform Team" owns CI/CD pipelines, infrastructure provisioning, and observability tooling.

### 3. Enabling Team
**Purpose**: Helps stream-aligned teams adopt new capabilities.

- Temporary engagement model (weeks to months, not permanent)
- Coaches and teaches, doesn't take over
- Fills skill gaps while building internal capability
- Disbands or moves on when the capability is transferred

**Example**: "SRE Enablement" helps a team adopt on-call practices, then moves to the next team.

### 4. Complicated Subsystem Team
**Purpose**: Owns a component that requires deep specialist knowledge.

- Only needed when the subsystem is too complex for a stream-aligned team to own
- Provides an API or service that other teams consume
- Small team of specialists

**Example**: "ML Model Team" owns recommendation algorithms consumed by multiple product teams.

## Interaction Modes

| Mode | Description | Between |
|------|-------------|---------|
| **Collaboration** | Two teams work closely together for a period | Stream + Enabling, or Stream + Stream |
| **X-as-a-Service** | One team provides a service, the other consumes it | Platform -> Stream, Subsystem -> Stream |
| **Facilitating** | One team coaches the other | Enabling -> Stream |

## When to Restructure Teams

- Stream-aligned team is blocked waiting for another team > 25% of the time
- A team owns too many things and can't maintain quality
- Cross-team coordination is consuming more than 20% of leadership time
- A new product area or business line emerges

## Anti-Patterns

- **Component teams**: Organized around technical layers (frontend team, backend team, database team). Creates handoffs and blocks flow.
- **Project teams**: Assembled for a project, disbanded after. No ownership continuity.
- **Oversized teams**: 15+ people can't coordinate effectively. Split them.
- **Teams of one**: Not a team. Fragile and isolating. Minimum viable team is 3.

## See Also

- `research/platform-vs-embedded-model.md` -- Platform team deep dive
- `research/scaling-5-to-50.md` -- When to introduce topology changes
- `hub/cross-team-collaboration.md` -- Managing team interactions
- `decisions/team-structure-decision.md` -- Template for restructuring decisions
