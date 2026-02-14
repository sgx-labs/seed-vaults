---
title: "How Do I Prevent Burnout as a Solo Maintainer?"
tags: [burnout, sustainability, health, maintainer, boundaries]
content_type: research
domain: engineering
---

# How Do I Prevent Burnout as a Solo Maintainer?

## The Question

A 2024 survey found 60% of open source maintainers have considered quitting. Burnout is the leading cause. How do you maintain a project without destroying yourself?

## Warning Signs

Recognize these early. They escalate if ignored:

- Dreading opening GitHub notifications
- Guilt about not responding quickly enough
- Resentment toward users asking questions
- Working on the project from obligation, not interest
- Irritability when receiving feature requests
- Physical symptoms: poor sleep, tension, anxiety
- Feeling like the project owns you instead of the reverse

## Prevention Strategies

### 1. Set Boundaries From Day One

Write these in your README or CONTRIBUTING.md:

```markdown
## Response Times
- Issues: Reviewed within 1 week
- PRs: Reviewed within 2 weeks
- Security issues: Addressed within 48 hours
- Feature requests: Discussed, not guaranteed

This project is maintained in my spare time. Please be patient.
```

This is not rude. It is honest. It protects you and sets expectations.

### 2. Learn to Say No

You do not owe anyone features. Practice these responses:

- "Interesting idea, but this is outside the project's scope."
- "I do not have bandwidth for this right now. PRs welcome!"
- "This would be better as a separate project/plugin."

Saying no to one thing is saying yes to everything else, including your health.

### 3. Automate the Tedious Parts

| Task | Automation |
|------|-----------|
| Stale issues | GitHub Actions stale bot (close after 90 days) |
| CI/CD | GitHub Actions (never manually build releases) |
| Dependency updates | Dependabot (auto-PRs for updates) |
| Issue triage | Issue templates (structured bug reports) |
| Welcome messages | GitHub Actions welcome bot |

Every minute you automate is a minute you do not spend on maintenance drudgery.

### 4. Batch Your Open Source Time

Do not check GitHub notifications throughout the day. Set specific times:

- Monday and Thursday evenings: issue triage and PR review
- Saturday morning: feature development (if you feel like it)
- Turn off GitHub notifications on your phone

### 5. Share the Load

You do not have to do everything alone:

- Promote active contributors to co-maintainers
- Give triage access to trusted community members
- Document your processes so others can help
- Accept "good enough" contributions you would have done differently

### 6. Protect Your Motivation

- Work on what interests you, not just what users demand
- Celebrate milestones (releases, star counts, first contributors)
- Connect with other maintainers (they understand)
- Take breaks without announcing or apologizing
- Remember: the project exists to serve you, not the other way around

## If You Are Already Burning Out

1. **Step back** — Take a week off. The project will survive.
2. **Post a status update** — "Taking a break, will be back [date]." Users will understand.
3. **Lower your standards temporarily** — Close old issues, merge imperfect PRs, skip a release.
4. **Talk to someone** — Other maintainers, friends, a professional if needed.
5. **Consider handing off** — If you no longer want to maintain it, find someone who does.

## Related

- `hub/sustainability.md` — Sustainability overview
- `research/sustainability/funding-options.md` — Getting paid for your work
- `research/community/governance-models.md` — Sharing leadership
