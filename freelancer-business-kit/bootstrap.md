---
title: "Bootstrap — Freelancer Business Kit"
tags: [bootstrap, onboarding, session-start, critical]
content_type: hub
domain: business
pinned: true
---

# Bootstrap — Freelancer Business Kit

## Water the Seed

First time here? Initialize your seed vault:

```bash
cd freelancer-business-kit
same init        # Set up SAME with your preferred embedding provider
same reindex     # Index all notes — takes ~30 seconds
same search "how do I write a proposal"  # Verify it works
```

## First Session: Business Setup Interview

**This is a framework seed.** On your first session, the agent should ask discovery questions and personalize the vault for your practice. Start with:

```
You: "I'm setting up my freelance business. Interview me."
```

The agent will ask about:
1. What services you offer (dev, design, consulting, etc.)
2. Your target market and ideal client
3. Your current rate and pricing model
4. Your business entity (sole prop, LLC, S-Corp)
5. Tools you already use (invoicing, contracts, scheduling)
6. Your biggest business pain points

Answers get saved as notes in `clients/`, `decisions/`, or updates to templates.

## Where To Find Things

| Need | Search for | Or read directly |
|------|-----------|-----------------|
| Client management | `client onboarding red flags` | `hub/clients.md` |
| Writing proposals | `proposal win pricing` | `hub/proposals.md` |
| Contracts | `contract clause IP payment` | `hub/contracts.md` |
| Invoicing | `invoice late payment follow-up` | `hub/invoicing.md` |
| Tax guidance | `quarterly taxes LLC S-Corp` | `hub/taxes.md` |
| Growing business | `retainers portfolio referrals` | `hub/growth.md` |
| Proposal template | `proposal template` | `templates/proposal-template.md` |
| Contract template | `contract template` | `templates/contract-template.md` |
| Invoice template | `invoice template` | `templates/invoice-template.md` |
| SOW template | `scope of work template` | `templates/scope-of-work.md` |
| Client onboarding | `onboarding checklist` | `templates/client-onboarding.md` |

## How To Use This Seed

```bash
# Search for anything
same search "how do I handle scope creep"

# Via MCP in Claude Code
mcp__same__search_notes(query="firing a client professionally", top_k=5)

# Find related notes
mcp__same__find_similar_notes(path="research/clients/red-flags.md", top_k=3)
```

## Content Organization

- `hub/` — Category overviews and navigation. Start here for any topic.
- `research/` — Detailed guides. One question per file. The real substance.
- `entities/` — Living docs about tools (Stripe, FreshBooks, etc.). Update as you switch tools.
- `templates/` — Starting-point documents. Customize for your practice.
- `decisions/` — Business decisions log. Rate changes, tool switches, entity changes.
- `sessions/` — Session handoffs for continuity between work sessions.

## Personalization Checklist

After the first session, you should have:
- [ ] Your rate documented in `decisions/log.md`
- [ ] Your business entity decision logged
- [ ] At least one template customized with your info
- [ ] Your tool stack noted in relevant entity files
