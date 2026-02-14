---
title: "Freelancer Business Kit Seed Specification"
content_type: hub
tags: [seed-spec, freelancer, framework, business-kit, overview]
---

# Freelancer Business Kit Seed

## Metadata
- **Name**: freelancer-business-kit
- **Type**: Framework (scaffolding user fills with their business details)
- **Audience**: Freelance developers, designers, consultants, solopreneurs
- **Note count target**: 40-60
- **Agent build time**: 2-3 hours

## Purpose
The operational backbone for running a freelance business. From proposals and contracts to invoicing and client management. Your agent knows the business patterns so you can focus on the work.

## Hub Categories

1. hub/clients.md — Client management, communication, expectations, red flags
2. hub/proposals.md — Writing proposals, pricing, scope definition, SOWs
3. hub/contracts.md — Contract templates, terms, IP ownership, liability
4. hub/invoicing.md — Invoicing, payment terms, late payments, tools
5. hub/taxes.md — Tax basics, quarterly estimates, deductions, entity structure
6. hub/growth.md — Finding clients, referrals, portfolio, personal brand

## Key Questions

1. How do I write a proposal that wins projects?
2. What should my freelance contract include?
3. How do I set my hourly/project rate?
4. How do I handle scope creep?
5. How do I invoice clients and handle late payments?
6. What are the tax basics for freelancers (US)?
7. Should I be an LLC, sole prop, or S-Corp?
8. How do I fire a bad client professionally?
9. How do I build a portfolio that attracts premium clients?
10. What are the red flags in a client relationship?
11. How do I negotiate rates without losing the deal?
12. How do I manage multiple projects without dropping balls?
13. How do I write a scope of work (SOW)?
14. What tools should I use (invoicing, contracts, time tracking)?
15. How do I transition from full-time to freelance?
16. How do I handle revisions and feedback loops?
17. How do I build recurring revenue as a freelancer?
18. How do I set boundaries with clients?
19. How do I get testimonials and case studies?
20. When should I raise my rates?

## Entities

1. entities/stripe.md — Payment processing for freelancers
2. entities/freshbooks.md — Invoicing, expenses, time tracking
3. entities/upwork.md — Platform strategy, profile, bidding
4. entities/calendly.md — Scheduling, availability, meeting types

## CLAUDE.md Governance

- This is a framework — fill in your rates, terms, and client details
- When onboarding a new client, create a note in clients/
- Log business decisions in decisions/ (rate changes, tool switches, etc.)
- Templates in templates/ are starting points — customize for your practice
- Financial data stays local — never sync invoicing details

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

- **Large addressable market** — millions of freelancers worldwide
- **Framework seed = high retention** — users fill it with their business data, making it personal
- **Practical daily use** — proposals, invoices, client notes = frequent touchpoints
- **Low domain expertise needed** — universal business patterns, not niche knowledge
