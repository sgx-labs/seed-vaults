---
title: "How Do I Run a Soft Launch Before Going Public?"
tags: [soft-launch, feedback, beta, testing, launch, friction, user-testing]
content_type: research
domain: engineering
---

# How Do I Run a Soft Launch Before Going Public?

## The Question

Going straight to Show HN with an untested project is a gamble. How do you get real feedback before the public launch?

## What a Soft Launch Is

Share your project with 10-30 people before going public. The goal is not stars or publicity — it is finding friction before hundreds of people hit it simultaneously.

## Who to Include

### The Ideal Soft Launch Group (10-30 People)

1. **5-10 developers you know** — Friends, colleagues, former coworkers. People who will be honest.
2. **3-5 people in your target audience** — People who actually have the problem you solve.
3. **2-3 people outside your bubble** — Different OS, different experience level, different workflow.

### How to Ask

Do not say "please star my repo." Say:

```
Hey [name], I built a tool that [does X]. Would you mind spending
10 minutes trying it out? I want to know:

1. Did the install instructions work?
2. Was it clear what the tool does from the README?
3. Did you hit any friction trying the basic example?

Honest feedback is more helpful than encouragement. Tell me
what confused you.
```

## What to Look For

### Critical Issues (Fix Before Public Launch)

- Install instructions that fail on any platform
- README that does not explain what the project does
- Core functionality that crashes or produces wrong results
- Missing prerequisites not mentioned in the docs

### Important Issues (Fix If You Can)

- Confusing error messages
- Missing examples for common use cases
- Performance issues on typical workloads
- Unclear next steps after installation

### Nice-to-Have (Fix After Launch)

- Edge case bugs
- Feature requests
- Documentation for advanced features
- Platform-specific quirks

## Running the Feedback Process

### Day 1-2: Send Invitations
Send personal messages to your soft launch group. Include:
- Link to the repo
- What you want feedback on (specific questions)
- A deadline ("By Friday would be great")

### Day 3-5: Collect Feedback
- Track feedback in a simple document or GitHub issues
- Note which issues multiple people hit (those are priorities)
- Watch for patterns, not individual opinions

### Day 6-7: Fix and Polish
- Fix every install/setup issue that was reported
- Improve the README based on confusion points
- Update error messages that tripped people up
- Re-test everything on a clean machine

### Day 8+: Public Launch
You are now launching a project that has been tested by real users. Your install instructions work. Your README makes sense. You are ready.

## The Minimum Viable Soft Launch

If you do not have time for a full process, do this:

1. Ask ONE person to install it on a clean machine while you watch
2. Do not help them — just observe where they get stuck
3. Fix everything they struggled with
4. Repeat with one more person

Two rounds of this catches 80% of friction.

## Related

- `hub/launch.md` — Full launch checklist
- `research/launch/first-100-stars.md` — After launch, building traction
- `research/docs/writing-install-instructions.md` — Install docs that work
