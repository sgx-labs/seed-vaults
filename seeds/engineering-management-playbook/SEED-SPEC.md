---
title: "Engineering Management Playbook — Seed Spec"
tags: [seed-spec, planning, structure, quality-checklist, engineering-management]
content_type: hub
domain: engineering-management
---

# Engineering Management Playbook — Seed Spec

## Metadata
- **Name**: engineering-management-playbook
- **Type**: Knowledge
- **Audience**: Engineering managers, tech leads, directors, VPs
- **Note count target**: 55-65
- **Agent build time**: 6-8 hours

## Purpose

Comprehensive reference for engineering managers who need actionable frameworks for every aspect of people management and technical leadership. Covers 1-on-1s, hiring, firing, performance reviews, delegation, team health, incident management, sprint retros, onboarding, managing up, cross-team collaboration, meeting management, metrics, and psychological safety. Every note should help a manager make a better decision or navigate a difficult situation.

## Hub Categories

1. hub/one-on-one-framework.md -- Structured 1-on-1 meeting system with career, blockers, feedback, action items
2. hub/performance-review-writing.md -- Evidence-based, growth-oriented performance review guide
3. hub/hiring-rubric.md -- Technical + culture scoring system with interview question bank
4. hub/pip-and-separation.md -- Compassionate but clear PIP and separation process
5. hub/delegation-framework.md -- What to delegate, how, when to take back
6. hub/team-health-indicators.md -- Leading indicators for team health, not lagging
7. hub/technical-decision-making.md -- RFC process, ADR format, when to decide vs gather
8. hub/incident-management.md -- Severity levels, roles, postmortem template
9. hub/sprint-retrospectives.md -- 5 proven retrospective formats
10. hub/onboarding-engineers.md -- 30-60-90 day plan for new engineers
11. hub/managing-up.md -- Communicating with your VP/CTO effectively
12. hub/cross-team-collaboration.md -- Dependencies, SLAs, stakeholder management
13. hub/meeting-management.md -- When to meet, when to async, how to run effective meetings
14. hub/engineering-metrics.md -- DORA, cycle time, what NOT to measure
15. hub/psychological-safety.md -- Concrete actions for building trust and safety

## Key Questions (agent research targets)

1. How do I run effective 1-on-1s that my reports actually value?
2. How do I write performance reviews that drive growth?
3. How do I build a fair, consistent hiring process?
4. How do I handle underperformance compassionately?
5. What should I delegate and what should I keep?
6. How do I know if my team is healthy before problems surface?
7. When should I make a technical decision vs let the team decide?
8. How do I run a blameless postmortem that actually prevents recurrence?
9. Which retrospective format works best for my team's situation?
10. How do I set up a new engineer for success from day one?
11. How do I communicate effectively with my skip-level leadership?
12. How do I manage dependencies across teams without constant firefighting?
13. Are we having too many meetings or not enough?
14. Which engineering metrics actually matter and which are vanity metrics?
15. How do I create an environment where people speak up about problems?

## Entities

1. entities/engineering-levels-matrix.md -- IC1-IC7, M1-M5 with expectations
2. entities/meeting-cadences.md -- Common cadences for different team sizes
3. entities/interview-question-bank.md -- Behavioral, system design, coding questions
4. entities/feedback-frameworks.md -- SBI, COIN, radical candor quick reference
5. entities/dora-metrics-reference.md -- DORA metrics definitions and benchmarks
6. entities/agile-ceremonies-reference.md -- Sprint ceremonies quick reference
7. entities/engineering-okr-examples.md -- OKR examples for engineering teams
8. entities/team-topology-patterns.md -- Team topology patterns reference
9. entities/adr-template.md -- Architecture Decision Record template
10. entities/stakeholder-communication-templates.md -- Email and doc templates

## Decisions to Pre-Populate

1. decisions/log.md -- Decision log
2. decisions/team-structure-decision.md -- Team structure decision template
3. decisions/process-change-decision.md -- Process change decision template

## Water the Seed Protocol

### 1. Scan & Merge
- Agent reads existing CLAUDE.md before suggesting changes
- Proposes merged version -- shows diff, waits for approval
- Archives originals to `_archive/` before writing changes
- Never overwrites user work

### 2. Gap Analysis
- After indexing, compare seed coverage against user's actual team situation
- Search vault for each key question -- identify what's covered, what's missing
- Factor in team size, org structure, existing processes, and maturity level

### 3. Research Recommendations
- Present numbered list of recommended research topics based on gaps
- Offer to spin up parallel research agents for approved topics
- New research gets saved as notes in the user's vault

### 4. SAME Showcase Behaviors
- Use `same search` to find answers before generating from scratch
- Use `same save_decision` when the user makes a management decision
- Use `same create_handoff` at session end
- Use `same find_similar_notes` to cross-reference related frameworks
- Use `same search_across_vaults` when the user has multiple seeds
- Surface these capabilities naturally -- don't lecture, demonstrate

## Config Notes

```toml
[vault]
  path = "."
  skip_dirs = ["_Raw", "_archive"]
  handoff_dir = "sessions"
  decision_log = "decisions/log.md"

[memory]
  composite_threshold = 0.65
  max_results = 4
```

## Quality Checklist
- [ ] Every note under 200 lines
- [ ] YAML frontmatter on all notes (title, tags, content_type, domain)
- [ ] bootstrap.md with pinned: true and "Water the Seed" section
- [ ] CLAUDE.md with merge protocol and SAME showcase behaviors
- [ ] config.toml.example (provider-agnostic)
- [ ] No PII, no API keys, no secrets
- [ ] `_archive/` in skip_dirs
- [ ] `same reindex` produces 0 errors
- [ ] `same search` returns relevant results for 5 test queries
- [ ] Key questions are answerable by searching the vault
- [ ] SAME tools are demonstrated naturally in CLAUDE.md examples
