---
title: "Resume & Interview Prep Seed Specification"
content_type: hub
tags: [seed-spec, resume, interview, framework, job-search, overview]
---

# Resume & Interview Prep Seed

## Metadata
- **Name**: resume-interview-prep
- **Type**: Framework (scaffolding user fills with their experience and target roles)
- **Audience**: Job seekers, career changers, professionals updating their resume
- **Note count target**: 40-55
- **Agent build time**: 2-3 hours

## Purpose
Complete toolkit for landing your target role. From resume tailoring and ATS optimization to behavioral interview prep and salary negotiation. Your agent knows the hiring patterns so you can focus on presenting your best self.

## Hub Categories

1. hub/resume.md — Resume writing, formatting, tailoring, ATS optimization, bullet points
2. hub/cover-letter.md — Cover letter strategy, structure, customization
3. hub/interview-prep.md — Behavioral, technical, and cultural fit interviews
4. hub/company-research.md — Researching target companies, culture, values, team
5. hub/networking.md — LinkedIn, referrals, informational interviews, warm intros
6. hub/salary.md — Salary negotiation, total comp, counter-offers, benefits

## Key Questions

1. How do I tailor my resume for a specific job posting?
2. What makes a resume ATS-friendly?
3. How do I write strong bullet points with quantified results?
4. How do I handle employment gaps on my resume?
5. How do I prepare for behavioral interviews using STAR method?
6. What questions should I ask the interviewer?
7. How do I research a company before an interview?
8. How do I negotiate salary without losing the offer?
9. When should I follow up after an interview?
10. How do I write a cover letter that gets read?
11. How do I leverage LinkedIn for job searching?
12. How do I get a referral at a target company?
13. How do I handle the "tell me about yourself" question?
14. How do I explain a career change on my resume?
15. What should I wear / how should I present on video calls?
16. How do I prepare for technical or case interviews?
17. How do I handle salary history questions?
18. How do I evaluate a job offer beyond salary?
19. How do I write a thank-you email after an interview?
20. How do I manage multiple applications and track progress?

## Entities

1. entities/linkedin.md — LinkedIn optimization for job seekers
2. entities/ats-systems.md — Applicant Tracking Systems and how to beat them

## CLAUDE.md Governance

- This is a framework — fill in your experience, target roles, and company details
- When targeting a new company, create a note in entities/
- Log job search decisions in decisions/ (role pivots, offer evaluations, etc.)
- Templates in templates/ are starting points — customize for each application
- Personal details stay local — never sync salary info or sensitive career data

## Config Notes

```toml
[vault]
  path = "."
  skip_dirs = ["_Raw", "templates"]
  handoff_dir = "sessions"
  decision_log = "decisions/log.md"

[memory]
  max_token_budget = 1200
  max_results = 4
  composite_threshold = 0.65
```

## Why This Seed

- **Universal need** — everyone needs a resume at some point
- **Framework seed = high personalization** — users fill it with their own experience and targets
- **High-stakes, time-sensitive** — job searching has deadlines and emotional weight
- **Practical daily use** — resume edits, interview prep, follow-ups = frequent touchpoints
