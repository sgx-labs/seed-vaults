---
title: "Bootstrap — Your Personal Operating System"
tags: [bootstrap, onboarding, session-start, critical, flagship]
content_type: hub
domain: productivity
pinned: true
---

# Bootstrap — Your Personal Operating System

## What This Is

This is an AI-powered personal operating system that grows with you. It tracks your projects, logs your decisions, manages your goals, monitors your energy, and runs your reviews — all searchable, all persistent across sessions. Your agent learns who you are through conversation and keeps your system running without you having to maintain it.

But it is more than a productivity system. It is a knowledge base that compounds. Every session makes the next session smarter. Every decision logged is a decision you never re-litigate. Every research note you add connects to others automatically. The longer you use it, the more valuable it becomes.

This seed works best with the [SAME engine](https://github.com/sgx-labs/same), which gives your AI agent persistent memory, semantic search, and session continuity. But the notes and structure are useful on their own.

---

## Water the Seed — Getting Started

### Step 1: Initialize

```bash
same init && same reindex
```

This indexes every note in the vault so your agent can search them. If you are using Claude Code with SAME's MCP server, the agent will pick up `bootstrap.md` automatically at session start because it is pinned.

### Step 2: The Discovery Interview

**If entity files are empty templates, the agent runs discovery.** This is not a form to fill out. It is a conversation. The agent asks, listens, and builds your personal OS from your answers.

#### Express Setup (3 questions, 5 minutes)

Start here. These three questions give the agent enough to build your initial entity files and start being useful immediately:

1. **What do you do?** — Your role, work context, and what a typical day looks like.
2. **What is your top priority right now?** — The one project or goal that matters most this week or month.
3. **When do you do your best focused work?** — Morning, afternoon, evening, or it varies. This sets your energy map.

That is enough to get started. The agent will populate your entity files, set up your project tracker, and begin surfacing relevant research notes. Everything else can be learned gradually over the next few sessions.

#### Full Discovery (optional, 20-30 minutes)

Want to do a deeper dive? This takes 20-30 minutes and helps the agent understand you much better. Or you can skip this entirely and let the agent learn these things gradually over the next few sessions — it will ask when the moment is right.

**About You**
- What are your strengths — the things that come easily? What drains you?
- How do you prefer to organize your time? Structured blocks, flexible flow, something in between?
- Are there aspects of focus, task initiation, or executive function that you find challenging? (Ask gently. Do not assume anything. Many people have strategies they have developed over years that work for them.)

**About Your Work**
- What are your 2-5 active projects right now?
- For each: what is the goal, where are you, and what is the next step?
- Who are the key people you work with? What are those relationships like?
- What tools do you live in daily?
- Where do things fall through the cracks?

**About Your Goals**
- What are you trying to achieve this quarter?
- What does success look like in 6 months? In a year?
- What is the one thing that, if you accomplished it, would make everything else easier?
- What have you been meaning to do but keep putting off?

**About Your Energy and Focus**
- What activities give you energy? What depletes it?
- How do you recover from a hard day or week?
- Do you notice patterns in your productivity — time of day, day of week, types of tasks?
- What does your ideal workday look like versus your actual workday?

**About Your Systems**
- Do you currently do any kind of regular review? What works about it?
- What productivity approaches have you tried? What stuck, what did not?
- What recurring decisions drain your energy?
- Is there anything about how your brain works that you wish productivity tools accounted for?

### Step 3: Populate

After the interview, the agent creates personalized files:

- **`entities/me.md`** — Who you are, how you work, your strengths and challenges
- **`entities/work.md`** — Your role, team, tools, and work patterns
- **`entities/projects.md`** — Your active projects with goals, status, and next actions
- **`current-state/projects.md`** — Live project tracker, updated every session
- **`current-state/goals.md`** — Goal progress, reviewed quarterly

### Step 4: Dashboard Setup

Based on your interview answers, the agent creates your initial dashboards from templates:

- **Weekly Dashboard** — Your command center, updated every Monday, reviewed Friday
- **Project Tracker** — Master view of everything you are working on

Templates live in `templates/dashboards/`. The agent picks the right ones and fills them with your real data.

### Step 5: The Research Cascade

This is where the seed comes alive. Based on what the agent learned about you:

1. **It recommends relevant research notes.** If you mentioned struggling with task initiation, it surfaces `research/adhd/task-initiation.md`. If you said you want to do quarterly planning, it finds `research/goals/quarterly-planning.md`.
2. **Each note suggests more.** At the bottom of every research note, there are links to related topics. Read one, discover three.
3. **Gaps become research tasks.** If you described a challenge the vault does not have a note for, the agent offers to research it and write a new note. That note then connects to the existing web of knowledge.
4. **The cascade never stops.** Every session, the agent looks for new connections between what you are doing and what the vault knows. It surfaces relevant research at the moment you need it — not as homework, but as insight.

This is the "Water the Seed" protocol. You plant with the interview. You water with every session. The seed grows into something uniquely yours.

---

## Where to Find Things

| What You Need | Where to Look | Direct Path |
|---------------|---------------|-------------|
| Getting started | You are reading it | `bootstrap.md` |
| Active projects | Search "project status" | `hub/projects.md` |
| Weekly review | Search "weekly review template" | `hub/reviews.md` |
| Goal tracking | Search "goals quarterly progress" | `hub/goals.md` |
| Past decisions | Search "decision why chose" | `hub/decisions.md` |
| Energy and focus | Search "energy burnout focus" | `hub/energy.md` |
| Productivity systems | Search "capture organize review" | `hub/systems.md` |
| Current priorities | Search "what am I working on" | `current-state/projects.md` |
| ADHD/ADD support | Search "executive function adhd" | `research/adhd/` |
| Focus and task initiation | Search "task initiation strategies" | `research/adhd/` |
| Dashboard templates | Search "weekly dashboard tracker" | `templates/dashboards/` |
| How SAME helps you | Search "same agent productivity" | `research/same-integration/` |
| Decision frameworks | Search "decision framework" | `research/decisions/` |
| Review methodologies | Search "review consistency" | `research/reviews/` |
| Goal-setting approaches | Search "okr quarterly habits" | `research/goals/` |
| Deep work and flow | Search "deep work flow state" | `research/energy/` |

---

## Key Questions This Seed Can Answer

### Productivity
1. What are my active projects and their current status?
2. What should I focus on right now based on my priorities and energy?
3. What decisions have I made recently and why?
4. What do I need to follow up on this week?
5. What patterns am I seeing across my work?
6. How did this week compare to last week?

### Goals and Planning
7. What are my goals this quarter and how am I tracking?
8. What is the next milestone for each project?
9. What have I been putting off and why?
10. What should I say no to?

### Energy and Focus
11. When do I do my best work and am I scheduling accordingly?
12. What is draining my energy and what can I change?
13. How can I protect my deep work time?

### Executive Function and ADHD Support
14. What strategies work for getting started on tasks I keep avoiding?
15. How can I work with my brain instead of against it?
16. What external systems can compensate for working memory challenges?
17. How do I handle days when nothing seems to work?

### Systems and Reviews
18. What does an effective 15-minute weekly review look like?
19. Where are things falling through the cracks in my current system?
20. What is the minimum viable system that actually works for me?

---

## How to Use This Vault

```bash
# Search for anything in natural language
same search "what did I decide about the hiring plan"

# Via MCP — the agent uses these automatically
search_notes(query="active project blockers", top_k=5)

# Find related notes — discover connections you did not know existed
find_similar_notes(path="research/adhd/executive-function.md", top_k=5)

# Log a decision so you never re-litigate it
save_decision(title="Chose vendor B", body="Better support, 10% cheaper", status="accepted")

# Save a handoff so the next session picks up exactly where you left off
create_handoff(summary="Finished Q1 planning", pending="Need to set OKRs", blockers="Waiting on budget")
```

---

## Content Organization

```
personal-productivity-os/
├── bootstrap.md                 # You are here — orientation and onboarding
├── CLAUDE.md                    # Agent governance and behavior rules
├── hub/                         # Category overviews — start here for any topic
├── research/                    # Frameworks, patterns, and evidence-based strategies
│   ├── adhd/                    # ADHD/ADD: executive function, strategies, accommodations
│   ├── same-integration/        # How SAME features map to productivity needs
│   ├── systems/                 # Capture, organize, review, execute
│   ├── reviews/                 # Review methodologies and consistency
│   ├── goals/                   # Goal-setting, habits, planning frameworks
│   ├── energy/                  # Focus, deep work, burnout prevention
│   └── decisions/               # Decision frameworks and cognitive load
├── entities/                    # Living docs about you, your work, your projects
├── decisions/                   # What you decided and why — prevents re-litigation
├── current-state/               # Live snapshot of projects and goals
├── templates/                   # Reusable templates for reviews, dashboards, kickoffs
│   └── dashboards/              # Weekly dashboards and project trackers
├── sessions/                    # Agent session handoffs — continuity across sessions
└── _Raw/                        # Full-length docs, exports — not indexed
```

---

## The Rhythm

| Cadence | What Happens | The Agent's Role |
|---------|-------------|-----------------|
| Every session | Agent loads context, checks priorities, surfaces relevant notes | Automatic via `get_session_context` |
| Daily | Quick capture, check priorities, log decisions as they happen | Agent updates `current-state/` files |
| Weekly | 15-minute review: wins, lessons, next week's theme | Agent prepares the review, you just talk |
| Monthly | Review themes, adjust goals, check if projects are still worth doing | Agent surfaces stale projects, asks hard questions |
| Quarterly | Set new goals, archive completed projects, recalibrate | Agent pulls data from past 12 weekly reviews |

---

## The Compound Effect

Session 1, this is a template. Session 10, it knows your projects. Session 50, it knows your patterns. Session 100, it knows you — how you work, when you struggle, what motivates you, which strategies actually work for your brain, and which ones sound good but never stick.

That is the difference between a productivity app and a personal operating system. Apps reset every time you open them. This remembers.
