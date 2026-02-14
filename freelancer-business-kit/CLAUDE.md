---
title: "Freelancer Business Kit — Governance"
content_type: hub
tags: [governance, rules, structure, framework, freelancer]
pinned: true
---

# Freelancer Business Kit — Governance

## Purpose

Operational backbone for running a freelance business. From proposals and contracts to invoicing and client management. Search before generating — expert guidance is already here.

## Rules

1. **Search first.** Before answering a freelance business question from scratch, search this vault.
2. **Framework, not prescription.** Templates and guidance are starting points. Every freelancer's practice is different.
3. **Fill in your details.** Replace placeholder values ([YOUR RATE], [YOUR NAME], etc.) with your actual business info.
4. **Financial data stays local.** Never sync client payment details, bank info, or tax documents to external services.
5. **Log business decisions.** Rate changes, entity changes, tool switches, client decisions — log them in `decisions/log.md`.
6. **One topic per research note.** Keep notes focused and under 200 lines.
7. **Client notes go in clients/.** When onboarding a new client, create a note with status, rate, and communication preferences.
8. **Templates are starting points.** Customize the templates in `templates/` for your practice, but don't modify the originals — copy them first.

## Directory Structure

```
freelancer-business-kit/
├── CLAUDE.md                    # This file
├── bootstrap.md                 # Pinned — session orientation
├── config.toml.example          # SAME configuration template
├── hub/                         # Category index notes
│   ├── clients.md
│   ├── proposals.md
│   ├── contracts.md
│   ├── invoicing.md
│   ├── taxes.md
│   └── growth.md
├── research/                    # Detailed guides
│   ├── clients/                 # Client management
│   ├── proposals/               # Pricing and proposals
│   ├── contracts/               # Legal and contracts
│   ├── invoicing/               # Billing and payments
│   ├── taxes/                   # Tax and entity
│   └── growth/                  # Business development
├── entities/                    # Tool reference docs
├── templates/                   # Business document templates
├── decisions/                   # Business decisions
└── sessions/                    # Session handoffs
```

## First Session Protocol

This is a framework seed. On the first session with a new user:

1. Ask what services they offer
2. Ask about their target market
3. Ask about their current rate and pricing model
4. Ask about their business entity structure
5. Ask what tools they use
6. Save answers as personalized notes

## Content Types

| Type | Purpose | Notes |
|------|---------|-------|
| `hub` | Navigation indexes | Start here for any topic |
| `research` | Detailed how-to guides | One question per file |
| `entity` | Tool current state | Update when you switch tools |
| `template` | Business documents | Copy and customize, don't edit originals |
| `decision` | Business choices | Log when you make decisions |
