---
title: "Post-Mortem Facilitation Guide"
tags: [post-mortem, facilitation, meeting, blameless, culture]
content_type: research
domain: operations
---

# Post-Mortem Facilitation Guide

## The Question

How do I facilitate a post-mortem meeting that produces useful outcomes?

## Before The Meeting

### Choose a facilitator

The facilitator should NOT be the person who caused or fixed the incident. They need to be neutral. Their job is to guide the conversation, not contribute technical details.

### Prepare the timeline

Draft a timeline before the meeting. Sources:

```bash
# Monitoring alert history
# Check PagerDuty incident timeline
pd incident list --since "2026-02-13T00:00:00Z" --until "2026-02-14T00:00:00Z"

# Git commits during the incident
git log --oneline --after="2026-02-13T00:00:00" --before="2026-02-14T00:00:00"

# Deployment logs
kubectl rollout history deployment/api-server -n production

# Chat logs from incident channel
# Export from Slack: #incident-YYYY-MM-DD
```

### Invite the right people

- Everyone who responded to the incident
- Service owners of affected systems
- Optional: engineering leadership (as listeners, not participants)
- **Do not invite:** People who will assign blame

## Meeting Agenda (60 Minutes)

### Opening (2 min)

Facilitator says:

> "This is a blameless post-mortem. We are here to learn from what happened and prevent it from happening again. We will focus on systems and processes, not individuals. Everything discussed here stays factual and constructive."

### Timeline Walkthrough (15 min)

Share the prepared timeline on screen. Walk through it chronologically.

**Facilitator prompts:**
- "Does anyone have corrections or additions to this timeline?"
- "What was happening at [time]? What information did we have?"
- "Were there any decisions that seemed right at the time but turned out differently?"

**Rules:**
- No interrupting
- No "you should have" statements
- Add detail but do not editorialize

### Impact Assessment (5 min)

Fill in the numbers together:

```
Users affected:     ___
Duration:           ___ minutes
Revenue impact:     $___
Error budget used:  ___%
Data impact:        ___
```

### Contributing Factors Discussion (20 min)

**Facilitator prompts:**
- "What conditions existed that allowed this to happen?"
- "What would have needed to be different to prevent this?"
- "Were there any earlier warning signs we missed?"
- "What assumptions did we have that turned out to be wrong?"

**Apply Five Whys to systems:**
```
"The service went down."
→ Why? "Database connection pool was exhausted."
→ Why? "A new endpoint had no connection limit."
→ Why? "Our code review checklist does not include connection pool impact."
→ Why? "We have never had a connection pool issue before."
→ Root: "No proactive checks for resource pool exhaustion in new code."
```

**If someone starts blaming:**
> "I want to redirect us to the system level. Rather than who did what, let us ask: what in our system allowed this to happen?"

### What Went Well (5 min)

This is mandatory. It reinforces good practices.

**Facilitator prompts:**
- "What prevented this from being worse?"
- "What part of our response worked well?"
- "Were there any tools or processes that helped?"

### Action Items (15 min)

For each contributing factor, identify one concrete fix.

**Facilitator prompts:**
- "What is the single most impactful thing we can do to prevent this?"
- "Who is the right owner for this action item?"
- "What is a realistic deadline?"

**Each item must have:** Owner, deadline, ticket number.

### Closing (2 min)

> "Thank you everyone. I will finalize the document and share it within 48 hours. Action items will be tracked in [issue tracker]. Any questions?"

## After The Meeting

1. Finalize the post-mortem document within 48 hours
2. Create tickets for all action items within 24 hours
3. Share the document with the engineering organization
4. Follow up on action items weekly until all are complete

## Related

- `research/postmortems/blameless-postmortem.md` — Blameless principles
- `research/postmortems/effective-action-items.md` — Writing good action items
- `templates/postmortem.md` — Post-mortem template
- `hub/post-mortems.md` — Post-mortem overview
