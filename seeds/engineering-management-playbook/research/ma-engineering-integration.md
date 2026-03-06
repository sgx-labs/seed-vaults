---
title: "M&A Engineering Integration"
tags: [merger, acquisition, integration, consolidation, culture, migration, due-diligence]
content_type: research
domain: engineering-management
---

# M&A Engineering Integration

## The Engineering Manager's Role in M&A

When your company acquires (or is acquired by) another, the engineering integration is usually the hardest part. Two codebases, two cultures, two sets of processes, and a room full of people worried about their jobs. Your role: execute the technical integration while preserving the humans.

## Phase 1: Due Diligence (Before the Deal Closes)

If you're involved before the acquisition closes:

**Technical Assessment:**
- Architecture overview: monolith vs microservices, cloud vs on-prem, languages, frameworks
- Technical debt level: how healthy is the codebase? How much undocumented tribal knowledge exists?
- Infrastructure: hosting, CI/CD, monitoring, security posture
- Data: schemas, data quality, PII handling, compliance (GDPR, SOC2)

**People Assessment:**
- Who are the key engineers? (The ones who leave and everything breaks)
- What's the team culture? (Move fast vs careful, top-down vs autonomous)
- What retention risk exists? (Vesting cliffs, burnout, competing offers)
- What's the management structure? (Will managers have roles post-acquisition?)

## Phase 2: Stabilize (First 60 Days)

**Rule #1: Change nothing for the first 30 days.** The acquired team is anxious. Forcing immediate changes ("now use our tools") creates resistance and departures.

**Do immediately:**
- Communicate clearly: what's changing, what's not, what's undecided
- Meet every engineer 1-on-1. Listen. Ask: "What are you worried about?"
- Identify the "bus factor" people and prioritize their retention
- Maintain their existing processes, tools, and deployments
- Assign integration buddies (one from each side, peer level)

**Don't do:**
- Announce a "rewrite everything on our stack" plan
- Eliminate their team's processes in favor of yours
- Make org structure decisions before understanding their team
- Promise things you can't guarantee (job security, title equivalence)

## Phase 3: Integrate (60-180 Days)

**Technical Integration Approaches:**

1. **Gradual convergence** (recommended): Pick the highest-value integration points first. Shared auth, shared data pipeline, unified monitoring. Leave the rest for later.

2. **Big bang migration**: Rewrite their system on your stack. Extremely risky, takes longer than estimated, destroys institutional knowledge. Avoid unless their system is truly end-of-life.

3. **Permanent coexistence**: Two systems, two stacks, minimal integration. Appropriate when the products serve different markets and deep integration isn't needed.

**Cultural Integration:**
- Mixed teams on new projects (people from both sides working together)
- Shared retros to surface friction points
- Combined on-call rotations (builds mutual respect quickly)
- Acknowledge what each team does better. Adoption should go both ways.

## When NOT to Use This Framework

- **Acqui-hires** (you bought the team, not the product): Skip the technical integration. Absorb the people into your teams and sunset their product.
- **When the acquired company stays independent**: Some acquisitions preserve autonomy intentionally. Don't integrate what's meant to stay separate.
- **If you're the one being acquired**: Your agency is limited. Focus on what you can control: supporting your team, preserving documentation, and being a good partner.

## In Reality

The number one cause of failed integrations: departures. Key engineers leave because they feel disrespected, undervalued, or uncertain. Every integration decision should be evaluated through the lens of retention.

The number two cause: hubris. "Our way is better" applied to everything. The acquired team has hard-won knowledge. Some of their approaches are better than yours. Have the humility to recognize it.

## See Also

- `research/scaling-5-to-50.md` -- M&A often triggers scaling challenges
- `research/engineering-culture-manifestos.md` -- Merging two cultures
- `hub/onboarding-engineers.md` -- Onboarding acquired engineers
- `hub/team-health-indicators.md` -- Monitoring health during integration
- `hub/cross-team-collaboration.md` -- Cross-team coordination post-merger
