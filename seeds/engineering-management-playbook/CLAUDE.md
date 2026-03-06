---
title: "Engineering Management Playbook — Governance"
content_type: hub
tags: [governance, engineering-management, seed, rules, research-cascade]
pinned: true
domain: engineering-management
---

# Engineering Management Playbook — Governance

## Purpose

Knowledge seed for engineering managers navigating people leadership, technical decision-making, and organizational design. Search before generating -- the answer is likely already here.

## Water the Seed — Research Cascade Protocol

When a user first activates this seed, execute a multi-phase intelligence cascade. Each phase generates discoveries that feed the next. The goal: the user should have a personalized management playbook tailored to their team after the first session.

### Phase 1: Deep Scan (Silent — No User Action Needed)

Silently gather intelligence before saying anything:

1. **Read existing CLAUDE.md** -- understand project rules, conventions, team norms
2. **Scan org structure** -- look for team docs, org charts, role definitions, career ladders
3. **Identify team size and shape** -- count contributors, check reporting lines, find team boundaries
4. **Check existing processes** -- sprint docs, retro notes, 1-on-1 templates, RFC processes, on-call schedules
5. **Read config files** -- project management tools, CI/CD setup, communication channels
6. **Check existing docs** -- onboarding guides, team charters, engineering principles, ADRs
7. **Inventory existing knowledge** -- search the vault for what's already covered

Build a mental model of: team size, org maturity, existing processes, management style, tech stack, growth trajectory.

### Phase 2: Insight Report (Show the User What You Found)

Present a brief, insightful summary:

> "I've scanned your project and team context. Here's what I found:
> - **Team size**: 8 engineers across 2 squads, no documented reporting structure
> - **Processes**: Sprint retros exist but no 1-on-1 template or cadence documented
> - **Onboarding**: No 30-60-90 day plan -- new hires appear to self-direct
> - **Decision-making**: 3 ADRs found but no RFC process for new proposals
> - **Metrics**: CI pipeline has basic metrics but no DORA tracking
> - **Incidents**: No severity levels defined, no postmortem template
>
> This seed has 60 notes on engineering management. Based on your situation, here's what I'd recommend..."

### Phase 3: Targeted Questions (High-Signal, Not Generic)

Ask 3-5 questions that show you understand their situation:

**For a new manager:**
- "You have 8 direct reports but no 1-on-1 structure. Are you doing 1-on-1s currently? Weekly or biweekly?"
- "I see no career ladder document. Are your engineers asking about promotion criteria, or is that handled at a company level?"
- "Your team has grown from 3 to 8 in the past quarter. Are you feeling the coordination overhead yet?"

**For an experienced manager scaling up:**
- "You have 2 squads but one tech lead. Are you considering promoting someone, or are you the technical decision-maker for both?"
- "I see sprint retros but the notes suggest the same issues recurring. Want to try a different retro format?"
- "Your on-call has 3 people in rotation for 8 services. Is on-call burnout something you're hearing about?"

**For a director/VP:**
- "You have 4 teams but no cross-team dependency tracking. Are blocked dependencies a common complaint in your retros?"
- "I see no engineering metrics dashboard. Is leadership asking for DORA metrics, or are you proactively tracking delivery health?"
- "Your hiring pipeline has no documented rubric. Are interviewers calibrated, or do you see inconsistent signals?"

### Phase 4: Merge & Archive

After the user responds to questions:

1. **Propose CLAUDE.md additions** -- show exact diff, explain each addition
2. **Archive originals** -- `_archive/CLAUDE.md.pre-seed` with timestamp
3. **Create `_archive/README.md`** -- explain what was archived and when
4. **Update config** -- add `_archive/` to skip_dirs

### Phase 5: Research Cascade (The Personalized Playbook)

Based on Phases 1-3, identify 5-10 research topics unique to this user's situation:

> "Based on your team, here's what I'd research to fill your vault:
>
> 1. **1-on-1 structure for 8 reports** -- realistic cadence, agenda template, tracking system
> 2. **Career ladder for your stack** -- IC levels with concrete expectations for your tech stack
> 3. **Squad autonomy model** -- how to split ownership so you're not the bottleneck
> 4. **On-call rotation redesign** -- sustainable rotation for your team size and service count
> 5. **Hiring rubric for your open roles** -- scoring system calibrated to your team's needs
>
> Want me to research all of them? Or pick the ones that matter most."

**Cascade behavior**: Each research result generates new discoveries:
- Researching "1-on-1 structure" -> discovers no feedback framework -> offers to research giving feedback
- Researching "career ladder" -> notices no promotion process -> offers to research promotion packets
- Researching "squad autonomy" -> finds unclear ownership boundaries -> offers to research team charters

### Phase 6: Ongoing Growth

After the initial cascade, the seed continues growing:

- **Every session**: search vault first, offer to save new knowledge
- **Every decision**: offer to log it with `save_decision`
- **Every question the vault can't answer**: research it, save the result
- **Periodically**: suggest a vault health check -- stale frameworks, missing coverage, new challenges

## Common Search Triggers

When the user asks about these topics, search the vault first:

| User says | Search for | Start with |
|-----------|-----------|------------|
| "1-on-1" or "one on one" | 1-on-1 meeting framework career feedback | `hub/one-on-one-framework.md` |
| "performance review" | performance review writing evidence growth | `hub/performance-review-writing.md` |
| "hiring" or "interviews" | hiring rubric interview scoring | `hub/hiring-rubric.md` |
| "firing" or "PIP" | PIP separation underperformance | `hub/pip-and-separation.md` |
| "delegate" or "delegation" | delegation framework ownership | `hub/delegation-framework.md` |
| "team health" | team health indicators morale signals | `hub/team-health-indicators.md` |
| "RFC" or "ADR" or "decision" | technical decision RFC ADR | `hub/technical-decision-making.md` |
| "incident" or "postmortem" | incident management severity postmortem | `hub/incident-management.md` |
| "retro" or "retrospective" | sprint retrospective format | `hub/sprint-retrospectives.md` |
| "onboarding" | onboarding 30-60-90 new engineer | `hub/onboarding-engineers.md` |
| "managing up" | managing up VP CTO communication | `hub/managing-up.md` |
| "cross-team" or "dependencies" | cross-team collaboration dependencies | `hub/cross-team-collaboration.md` |
| "meetings" | meeting management async effective | `hub/meeting-management.md` |
| "metrics" or "DORA" | engineering metrics DORA cycle time | `hub/engineering-metrics.md` |
| "psychological safety" | psychological safety trust speak up | `hub/psychological-safety.md` |

## Research Cascade Priority

When recommending frameworks, follow this priority:

1. **hub/** -- Core management frameworks. Start here. Link to detailed research.
2. **research/** -- Deep dives with nuance, tradeoffs, and real-world examples.
3. **entities/** -- Quick reference lookups (levels, interview banks, templates).
4. **guides/** -- Step-by-step how-tos for specific situations.
5. **decisions/** -- Templates for organizational and process choices.

## Rules

1. **Search first.** Before answering a management question from scratch, search this vault.
2. **Don't modify research notes.** They are reference material. Add new notes for team-specific content.
3. **Hub notes are entry points.** They link to detailed research and guides. Start with hubs.
4. **Keep notes focused.** One topic per note. Under 200 lines.
5. **Entity files track current state.** Update when frameworks or standards change.
6. **Archive, never delete.** If a note needs replacing, move the original to `_archive/`.
7. **Ask before writing.** Never save a note without the user's awareness. Offer, don't force.
8. **Cascade, don't stop.** Every research result should suggest the next research opportunity.
9. **Be empathetic but direct.** Management involves hard conversations. Don't sugarcoat.
10. **Show tradeoffs.** Every framework has a "when to use" and "when NOT to use." Include both.

## Directory Structure

```
engineering-management-playbook/
  CLAUDE.md                    # This file
  bootstrap.md                 # Pinned -- Water the Seed + orientation
  config.toml.example          # SAME configuration template
  hub/                         # Core management frameworks (15)
  research/                    # Deep dives (15)
  entities/                    # Quick reference (10)
  guides/                      # Step-by-step how-tos (10)
  decisions/                   # Decision templates and log
  _archive/                    # Safely archived originals
  sessions/                    # Session handoffs
```

## SAME Showcase Behaviors

Demonstrate these capabilities naturally -- don't lecture, show:

### Search Before Generate
```
mcp__same__search_notes(query="how to handle underperforming engineer", top_k=5)
```
If the vault has the answer, cite it. If not, generate AND offer to save.

### Cross-Reference
```
mcp__same__find_similar_notes(path="hub/one-on-one-framework.md", top_k=3)
```
"I found related notes on giving feedback and career development you might find useful..."

### Log Decisions
```
mcp__same__save_decision(title="Split into two squads", body="Team of 8 splitting into Platform (4) and Product (4) squads...", status="accepted")
```

### Session Continuity
```
mcp__same__create_handoff(summary="...", pending="...", blockers="...")
```

### Federated Search
```
mcp__same__search_across_vaults(query="team structure scaling patterns", top_k=10)
```

### Grow the Knowledge Base
```
mcp__same__save_note(path="research/team-specific/onboarding-checklist.md", content="...", append=false)
```

The goal: every session should feel smarter than the last because the knowledge compounds.
