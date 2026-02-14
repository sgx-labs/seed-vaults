---
title: "Discord — Community Setup, Channels, and Moderation"
tags: [discord, entity, community, chat, moderation]
content_type: entity
domain: engineering
---

# Discord — Community Setup, Channels, and Moderation

## What It Is
Real-time chat platform widely used for open source project communities. Free for unlimited members. Good for discussions, support, and building a sense of belonging around your project.

## When to Set Up Discord

- You have more than 5 active users asking questions
- GitHub Discussions feels too slow for your community
- You want real-time interaction with your users
- You want a place for casual discussion beyond issues/PRs

Do NOT create a Discord server if you have fewer than 5 active users. An empty server feels dead and discourages participation. Use GitHub Discussions first.

## Recommended Channel Structure

Start minimal. Add channels only when conversations need separation.

```
# Information
├── #welcome          — Server rules, getting started links
├── #announcements    — Releases, important updates (read-only)

# Community
├── #general          — Open discussion
├── #help             — Support questions
├── #show-and-tell    — Users share what they built

# Development (if you have contributors)
├── #contributing     — Discussion about contributing
├── #bugs             — Bug reports (link to GitHub Issues)
```

## Setup Checklist

- [ ] Server name and icon match your project branding
- [ ] Welcome channel with rules and quick-start links
- [ ] Roles: Admin, Maintainer, Contributor, Member
- [ ] Verification level: Medium (must have verified email)
- [ ] Slow mode on #general: 10 seconds (prevents spam)
- [ ] Announcement channel set to read-only for most users

## Moderation Approach

- **Be present** — Respond to questions, even if briefly
- **Be welcoming** — Greet new members, answer beginner questions
- **Be firm** — Remove spam, enforce code of conduct
- **Use bots sparingly** — Auto-moderation for obvious spam only
- **Pin useful answers** — Build a knowledge base over time

## Recommended Bots

| Bot | Purpose |
|-----|---------|
| MEE6 or Carl-bot | Welcome messages, basic moderation |
| GitHub bot | Notifications for releases and issues |
| ModMail | Private ticket system for user reports |

## Common Mistakes

- Creating too many channels too early (empty channels feel dead)
- No moderation plan (toxicity drives away good contributors)
- Not linking Discord from README and GitHub
- Letting Discord replace GitHub Issues (conversations get lost)

## Last Updated
February 2026
