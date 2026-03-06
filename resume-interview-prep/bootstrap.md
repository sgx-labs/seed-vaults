---
title: "Bootstrap — Resume & Interview Prep"
tags: [bootstrap, onboarding, session-start, getting-started, setup]
content_type: hub
domain: career
pinned: true
---

# Bootstrap — Resume & Interview Prep

## Water the Seed

First time here? Initialize your seed vault:

```bash
cd resume-interview-prep
same init        # Set up SAME with your preferred embedding provider
same reindex     # Index all notes — takes ~30 seconds
same search "how do I tailor my resume"  # Verify it works
```

## First Session: Career Discovery Interview

**This is a framework seed.** On your first session, the agent should ask discovery questions and personalize the vault for your job search. Start with:

```
You: "I'm preparing for a job application. Interview me."
```

The agent will ask about:
1. What role(s) you are targeting and at which companies
2. Your professional background and years of experience
3. Your key skills and accomplishments
4. Your biggest concerns (gaps, career change, relocation, etc.)
5. The specific job posting you are applying for
6. Your timeline and urgency

Answers get saved as notes in `entities/`, `decisions/`, or updates to templates.

## Where To Find Things

| Need | Search for | Or read directly |
|------|-----------|-----------------|
| Resume writing | `resume tailor bullet points` | `hub/resume.md` |
| Cover letters | `cover letter customize` | `hub/cover-letter.md` |
| Interview prep | `behavioral STAR technical` | `hub/interview-prep.md` |
| Company research | `company culture values` | `hub/company-research.md` |
| Networking | `LinkedIn referral intro` | `hub/networking.md` |
| Salary negotiation | `salary counter-offer comp` | `hub/salary.md` |
| Resume review checklist | `checklist review` | `templates/resume-review-checklist.md` |
| STAR story prep | `STAR story template` | `templates/star-story-template.md` |
| Interview prep sheet | `interview questions prep` | `templates/interview-prep-sheet.md` |
| Thank-you email | `thank you follow up` | `templates/thank-you-email.md` |

## How To Use This Seed

```bash
# Search for anything
same search "how do I handle employment gaps"

# Via MCP in Claude Code
mcp__same__search_notes(query="salary negotiation tactics", top_k=5)

# Find related notes
mcp__same__find_similar_notes(path="research/interview/star-method.md", top_k=3)
```

## Content Organization

- `hub/` — Category overviews and navigation. Start here for any topic.
- `research/` — Detailed guides. One question per file. The real substance.
- `entities/` — Living docs about target companies and tools. Update as you research.
- `templates/` — Starting-point documents. Customize for each application.
- `decisions/` — Job search decisions log. Role pivots, offer evaluations, timing.
- `sessions/` — Session handoffs for continuity between work sessions.

## Personalization Checklist

After the first session, you should have:
- [ ] Your target role and company documented
- [ ] Your key accomplishments captured for bullet points
- [ ] At least one STAR story drafted
- [ ] Your resume reviewed against the checklist
- [ ] A company research note for your target employer
