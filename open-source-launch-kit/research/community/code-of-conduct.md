---
title: "Why Do I Need a Code of Conduct and How Do I Enforce It?"
tags: [code-of-conduct, community, moderation, safety, contributor-covenant, enforcement]
content_type: research
domain: engineering
---

# Why Do I Need a Code of Conduct and How Do I Enforce It?

## The Question

Is a CODE_OF_CONDUCT.md just virtue signaling, or does it actually help? And what do you do when someone violates it?

## Why You Need One

A code of conduct is not about policing people. It is about setting expectations so your community feels safe. When expectations are clear, most people follow them. When they are not, bad actors claim ignorance.

Practical benefits:
- New contributors know what behavior is expected before joining
- You have grounds to remove toxic participants without drama
- It signals that you take community seriously (many contributors check for this)
- GitHub surfaces projects with codes of conduct in contributor search

## Which One to Use

**Contributor Covenant** (recommended): The most widely adopted code of conduct for OSS projects. Used by thousands of projects including Rails, Swift, and Go.

How to add it:
1. Go to your repo on GitHub
2. Click "Add file" > "Create new file"
3. Name it `CODE_OF_CONDUCT.md`
4. GitHub offers the Contributor Covenant as a template

## Enforcement Framework

Having a code of conduct without enforcement is worse than not having one. Here is a practical framework:

### Step 1: Document Reporting

Add to your CODE_OF_CONDUCT.md:
```
## Reporting
Report unacceptable behavior to [conduct@yourproject.dev].
All reports will be reviewed and investigated. We will maintain
confidentiality to the extent possible.
```

Use a dedicated email, not a personal one. This protects both you and the reporter.

### Step 2: Response Ladder

| Severity | Response | Example |
|----------|----------|---------|
| Minor | Private message, ask them to stop | Off-topic spam in issues |
| Moderate | Public warning, reference the policy | Dismissive or rude comments |
| Serious | Temporary ban (1-30 days) | Personal attacks, harassment |
| Severe | Permanent ban | Threats, doxxing, sustained harassment |

### Step 3: Documentation

Keep private records of incidents and responses. If a pattern emerges, you have documentation to support escalation.

## Common Scenarios

**"This is a waste of time / political correctness"**
Response: "We find that clear expectations help everyone contribute comfortably. The code of conduct exists so that everyone knows what to expect."

**Someone is technically helpful but interpersonally toxic**
This is the hardest case. A brilliant contributor who belittles others drives away ten contributors for every one they attract. Enforce the code of conduct consistently, regardless of technical contribution.

**False or exaggerated reports**
Investigate before acting. Talk to both parties privately. Most situations are miscommunication, not malice.

## Related

- `hub/community.md` — Community building overview
- `research/community/managing-issues-prs.md` — Day-to-day community management
