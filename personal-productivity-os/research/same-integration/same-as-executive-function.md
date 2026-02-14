---
title: "SAME as Executive Function Prosthesis"
tags: [same, integration, executive-function, cognition, adhd, memory]
content_type: research
domain: productivity
workstream: same-integration
---

# SAME as Executive Function Prosthesis

## The Thesis

Executive function is the set of cognitive processes that let you plan, focus, remember, and juggle multiple tasks. When executive function works well, you barely notice it. When it does not, everything falls apart: you forget commitments, re-litigate decisions, lose context between tasks, and spend half your energy just trying to figure out what you were doing.

SAME is not a productivity app. It is a cognitive prosthesis. Each of its features maps directly to an executive function that human brains perform unreliably.

## The Mapping

### Session Handoffs = Long-Term Memory

Your brain is supposed to carry forward what happened yesterday, what you decided last week, what is pending. It does this poorly. Important context evaporates between sessions.

`create_handoff` writes what happened, what is pending, and what is blocked into a persistent record. `get_session_context` retrieves it at the start of every session. This is long-term memory that actually works: no decay, no distortion, no "I think we discussed this but I can't remember the details."

The agent starts every session knowing exactly where you left off. You do not have to reconstruct context from scratch. That reconstruction cost — 10 to 30 minutes per session — is eliminated.

### Context Surfacing Hooks = Working Memory

Working memory is your brain's scratchpad: the handful of items you can hold in active attention. For most people, that is 4 to 7 things. For ADHD brains, it can be 2 to 3.

SAME's `UserPromptSubmit` hook fires every time you type something. It searches your vault and pushes relevant notes into the conversation. You do not have to remember that a related decision exists — the system finds it and surfaces it.

This is working memory without the capacity limit. Mention "quarterly planning" and the agent already has your goals, last quarter's review, and the planning template loaded. You never have to hold all of that in your head.

### Decision Logging = Decision Memory

Humans re-litigate settled decisions constantly. "Should we use this approach or that one?" — answered three weeks ago, but nobody remembers the reasoning. This is one of the most expensive failure modes in knowledge work.

`save_decision` captures what was decided, the reasoning, and what was rejected. Future searches surface the decision with full context. The agent can say: "This was decided on February 3rd. The reasoning was X. The alternative was Y, rejected because Z."

Decision memory means decisions stay decided. See: `research/same-integration/decision-memory.md`

### Federated Search = Associative Memory

The brain's best trick is association: you think of one thing and related things activate. But this fails across domains. Your work brain and your personal brain are separate. Ideas that connect across projects never meet.

`search_across_vaults` searches multiple vaults simultaneously. Work vault, personal vault, project vaults — all connected. You search for "deadline" and find work deadlines alongside personal commitments. You search for "design pattern" and find a software architecture note next to a graphic design note.

This is associative memory without domain walls.

### Pinned Notes = Prospective Memory

Prospective memory is remembering to do something in the future. It is the most fragile form of memory. "Remember to bring this up in the meeting." "Do not forget to follow up with that person." These intentions evaporate.

Pinned notes in SAME surface at the start of every session via `get_session_context`. They are prospective memory made durable. Pin something and it stays in front of you until you unpin it. No mental effort required to hold the intention.

### Session Recovery = Fault Tolerance

Brains crash. You get interrupted, your focus shatters, the power goes out, the app closes unexpectedly. Normal tools lose your state. You come back and have no idea where you were.

SAME's session recovery detects incomplete sessions — handoffs that were never written, sessions that ended abruptly. It reconstructs what was happening and presents it when you return. Your cognitive state survives crashes.

## Why This Matters

Every productivity system in history has asked you to compensate for your own cognitive limitations through discipline. Write things down. Check your list. Do the review. Build the habit.

SAME inverts this. The system compensates for you. It remembers, surfaces, connects, and persists — automatically, through hooks that fire without your intervention. You do not need discipline to use it. You just need to show up and talk to the agent.

That inversion is the difference between a tool you use and a cognitive function you have.

## See Also

- `research/same-integration/body-double-agent.md` — The agent as accountability partner
- `research/same-integration/context-surfacing-patterns.md` — Optimizing what gets surfaced
- `research/same-integration/vault-as-second-brain.md` — Why passive tools fail
- `research/systems/minimum-viable-system.md` — The simplest version of a system
