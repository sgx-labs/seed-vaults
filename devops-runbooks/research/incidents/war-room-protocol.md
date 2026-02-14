---
title: "War Room Protocol for Major Incidents"
tags: [war-room, incident, P0, coordination, bridge-call]
content_type: research
domain: operations
---

# War Room Protocol

## The Question

How do I run a war room during a major incident?

## When To Open A War Room

- Any P0 incident
- Any P1 incident lasting >30 minutes
- Any incident affecting multiple teams or services
- Any security breach

## Step 1: Open the war room (2 minutes)

```
1. Create incident channel: #incident-YYYY-MM-DD-brief-description
2. Start a video/voice bridge (Zoom, Google Meet, etc.)
3. Post the bridge link in the incident channel
4. Announce in #engineering:
   "WAR ROOM OPEN: [description]. Join: [channel] Bridge: [link]"
```

## Step 2: Assign roles

Three mandatory roles. One person, one role.

| Role | Responsibility | Who |
|------|---------------|-----|
| **Incident Commander (IC)** | Coordinates response, makes decisions, tracks timeline | Eng lead or senior SRE |
| **Tech Lead** | Drives debugging and fix implementation | Most knowledgeable engineer for affected system |
| **Communications Lead** | Status page, stakeholder updates, customer comms | Eng manager or designated communicator |

**IC announces:**
> "I am the Incident Commander. [Name] is Tech Lead. [Name] is Communications. Let's establish what we know."

## Step 3: Establish facts (5 minutes)

IC asks:

```
1. What is the user impact right now?
2. When did this start?
3. What changed recently? (deploys, config changes, traffic patterns)
4. What have we tried so far?
5. What is the current hypothesis?
```

## Step 4: Work the problem

**IC manages the process:**
- Assign investigation tasks: "Team A, check database. Team B, check the last deploy."
- Set timers: "Report back in 10 minutes."
- Prevent rat-holing: "We have been on this thread for 15 minutes. Let's try a different approach."
- Track decisions: Document every significant action in the incident channel.

**Tech Lead drives the fix:**
- Follow the relevant runbook
- Announce actions before taking them: "I am going to restart the database primary."
- Report results: "Restart complete. Checking connectivity now."

**Communications Lead handles updates:**
- Status page update every 30 minutes
- Stakeholder email for P0
- Customer support team briefed

## War Room Rules

1. **One conversation at a time.** IC controls the floor.
2. **Announce before acting.** "I am going to roll back the deploy."
3. **Facts, not speculation.** "Error rate is 15%" not "I think it might be the database."
4. **Mute when not speaking.** Background noise derails war rooms.
5. **No blame.** Save analysis for the post-mortem.
6. **Update the timeline.** Every significant event gets logged with a timestamp.

## Closing The War Room

When the incident is resolved:

```
IC announces:
"The incident is resolved. [Brief summary of what happened and what fixed it.]
Duration: X hours Y minutes.
Post-mortem will be scheduled within 48 hours.
Thank you everyone."
```

1. Update status page to "Resolved"
2. Send final stakeholder update
3. Close the bridge call
4. Keep the incident channel open for async follow-up
5. Assign post-mortem facilitator

## Related

- `research/incidents/incident-classification.md` — When to open a war room
- `research/incidents/escalation-procedures.md` — Escalation during war room
- `research/incidents/incident-communication.md` — Communication templates
- `hub/incident-response.md` — Incident response overview
