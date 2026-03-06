---
title: "Running a Blameless Postmortem"
tags: [guide, postmortem, blameless, incident, facilitation, learning, prevention]
content_type: guide
domain: engineering-management
---

# Running a Blameless Postmortem

## Before the Meeting

### Timing
- Schedule within 48 hours of incident resolution (memory fades fast)
- 60-90 minutes. Don't rush it.
- If the team is exhausted from the incident, wait one more day. But not more.

### Preparation
1. **Designate a facilitator** (not the incident commander or someone deeply involved -- they need to speak freely)
2. **Build the timeline**: Ask each participant to add events to a shared doc before the meeting
3. **Gather data**: Dashboards, logs, deploy history, Slack messages, alert timelines
4. **Set ground rules**: Share in the invite:
   - "This is a learning exercise, not a blame exercise"
   - "We assume everyone acted with the best information they had"
   - "We're here to improve the system, not judge individuals"

## Running the Meeting

### Opening (5 min)

Read the ground rules aloud. Then:

"The goal of this postmortem is to understand what happened, why it happened, and how we prevent it from happening again. We're going to focus on systems, not people. Let's start."

### Timeline Review (20 min)

Walk through the timeline chronologically. For each event, ask:

- "What was known at this point?"
- "What decision was made and why?"
- "What information was missing?"

**Critical**: When someone says "I should have..." interrupt gently. "At that moment, with the information you had, that was a reasonable action. What could the system have provided to help you make a different decision?"

### Contributing Factors (20 min)

Move from "what happened" to "why did it happen":

- "Why didn't monitoring catch this earlier?"
- "Why was this change possible without a test?"
- "Why didn't the runbook cover this scenario?"
- "Why did the alert go to the wrong team?"

Use the "5 Whys" technique for the most significant contributing factor. But don't overdo it -- 3 whys is usually enough.

**Anti-patterns to catch:**
- "Human error" is never a root cause. Ask: "What system allowed this error to happen?"
- "They should have known" is blame in disguise. Ask: "How could the system have made the right action obvious?"
- "We need to be more careful" is not an action item. Ask: "What automated check would prevent this?"

### Action Items (20 min)

For each contributing factor, generate concrete action items:

- **Each action item must be**: Specific, assigned to one owner, given a priority and due date
- **Good**: "Add alerting for database connection pool utilization > 80% — Owner: Alex — P1 — Due: Friday"
- **Bad**: "Improve monitoring" (not specific, no owner, no date)

Limit to 5-8 action items. More than that and nothing gets done.

### Lessons Learned (10 min)

Ask the room:
- "What surprised us?"
- "What went well in the response?"
- "What would we do differently next time?"

### Close (5 min)

- Read back the action items
- Confirm owners and due dates
- Announce where the postmortem doc will be published
- Thank everyone for their honesty

## After the Meeting

1. **Publish the postmortem** within 24 hours. Make it accessible to the whole engineering org.
2. **Track action items** in your normal task system (not just the doc -- they'll be forgotten)
3. **Follow up in 2 weeks**: Are the action items being completed?
4. **Reference in retros**: "The postmortem from the March incident identified X. Has it been addressed?"

## See Also

- `hub/incident-management.md` -- Full incident management framework with postmortem template
- `hub/psychological-safety.md` -- Why blameless requires active safety
- `hub/meeting-management.md` -- Facilitation skills
- `hub/team-health-indicators.md` -- Incident patterns as health signals
