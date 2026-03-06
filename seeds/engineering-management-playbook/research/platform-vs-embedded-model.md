---
title: "Platform Team vs Embedded Model"
tags: [platform, embedded, team-topology, organization, infrastructure, developer-experience]
content_type: research
domain: engineering-management
---

# Platform Team vs Embedded Model

## The Question

As your org grows, you face a fundamental design choice: do you create a dedicated platform/infrastructure team, or embed infrastructure expertise within product teams?

## The Platform Team Model

A separate team owns shared infrastructure: CI/CD, observability, developer tools, shared services, cloud infrastructure.

**Strengths:**
- Deep expertise: specialists who live and breathe infrastructure
- Consistency: one team enforces standards across the org
- Efficiency: shared solutions instead of every team building their own
- Career path: infrastructure engineers have a clear home

**Weaknesses:**
- Bottleneck risk: every team depends on them for changes
- Priority conflicts: platform team's priorities vs product team's needs
- Disconnect: platform team builds what they think is needed, not what product teams actually need
- "Ticket queue" antipattern: product teams submit requests and wait

**Works best when:** 20+ engineers, multiple product teams, significant shared infrastructure needs, enough demand to keep a dedicated team busy.

## The Embedded Model

Infrastructure expertise is distributed across product teams. Each team owns their own deployment, monitoring, and tooling.

**Strengths:**
- Fast feedback: the person who builds the feature also owns the infra
- No bottleneck: teams are self-sufficient
- Context-rich: infra decisions are informed by product needs
- Ownership: teams feel fully responsible for their systems

**Weaknesses:**
- Duplication: teams reinvent wheels independently
- Inconsistency: different teams use different tools, patterns, and standards
- Skill gaps: not every team has someone who can handle infrastructure well
- Lonely experts: the one infra-minded person on each team has no peers

**Works best when:** Small org (<20 engineers), product teams with strong generalists, simple infrastructure needs.

## The Hybrid: Platform-as-a-Product

The emerging best practice. A platform team exists but operates like a product team:

- Product teams are their customers
- They have a roadmap informed by customer interviews (with product teams)
- They provide self-service tools, not ticket queues
- Their success metric: "How fast can a product team ship without asking us for help?"

**Key principles:**
1. **Self-service first**: Product teams should be able to create a new service, set up monitoring, and deploy without filing a ticket.
2. **Paved roads, not railroads**: The platform provides a recommended path that's easy and fast. Teams CAN deviate, but the default is excellent.
3. **Inner-source contributions**: Product teams can contribute to platform tools. The platform team reviews and maintains.
4. **Embed temporarily**: Platform engineers embed in product teams for 2-4 week rotations to understand their pain.

## When NOT to Use This Framework

- **Very small teams (<10 engineers)**: You don't need a platform team. One person with infra skills can handle it alongside product work.
- **When you're forcing a model**: Don't create a platform team because "that's what big companies do." Create one when product teams are spending >20% of their time on infrastructure.
- **If your "platform" is one person**: That's not a team, it's a single point of failure with a fancy title.

## In Reality

Most platform teams start with good intentions and become ticket queues within a year. The antidote: treat developer experience as the platform team's product and measure their success by their customers' speed, not by their own output.

## See Also

- `entities/team-topology-patterns.md` -- Team topology patterns reference
- `research/scaling-5-to-50.md` -- When to create your first platform team
- `hub/cross-team-collaboration.md` -- Platform/product team interaction
- `research/developer-experience.md` -- DX as a priority for platform teams
- `hub/engineering-metrics.md` -- Measuring platform team effectiveness
