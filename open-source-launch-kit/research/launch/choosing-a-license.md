---
title: "How Do I Choose an Open Source License?"
tags: [license, legal, mit, apache, gpl, launch, agpl, bsd, copyleft]
content_type: research
domain: engineering
---

# How Do I Choose an Open Source License?

## The Question

Which license should you use? There are dozens, but the real choice is between three families.

## Decision Tree

1. **Want maximum adoption, minimum friction?** -> MIT License
2. **Want adoption but also patent protection?** -> Apache 2.0
3. **Want to prevent proprietary forks?** -> GPL 3.0 or AGPL 3.0

## License Comparison

| License | Permissions | Conditions | Patent Grant |
|---------|------------|------------|-------------|
| **MIT** | Use, modify, distribute, sell | Include license text | No |
| **Apache 2.0** | Use, modify, distribute, sell | Include license + NOTICE, state changes | Yes |
| **BSD 2-Clause** | Use, modify, distribute, sell | Include license text | No |
| **GPL 3.0** | Use, modify, distribute | Derivative works must also be GPL | Yes |
| **AGPL 3.0** | Use, modify, distribute | Network use counts as distribution | Yes |

## Detailed Breakdown

### MIT License (Recommended Default)
- One short paragraph. Everyone understands it.
- No restrictions on commercial use.
- No patent clause — but patent trolling of small OSS projects is extremely rare.
- Used by: React, Vue.js, Rails, jQuery, .NET

### Apache 2.0
- Includes an explicit patent grant — if a contributor owns patents covering their contribution, they grant a license to users.
- Includes a patent retaliation clause — if someone sues you for patent infringement, their license terminates.
- More complex text, but still permissive.
- Used by: Kubernetes, TensorFlow, Apache projects

### GPL 3.0 / AGPL 3.0
- Copyleft: derivative works must use the same license.
- GPL: triggered by distribution. AGPL: triggered by network use (SaaS).
- Prevents companies from taking your code proprietary.
- Can limit corporate adoption (legal teams are wary).
- Used by: Linux kernel (GPLv2), WordPress, Grafana (AGPLv3)

## Common Mistakes

1. **No license at all** — Without a license, nobody can legally use your code. This is worse than any license.
2. **Custom licenses** — "Do whatever you want" is not a real license. Use a standard one that lawyers understand.
3. **Changing licenses later** — Difficult once you have contributors. Each contributor owns copyright on their contributions.
4. **License in README only** — Put the full text in a LICENSE file at the root.

## How to Add a License

1. Go to github.com/yourorg/your-project
2. Click "Add file" -> "Create new file"
3. Name it `LICENSE`
4. GitHub will offer a license picker with full text

Or use choosealicense.com for a guided comparison.

## Related

- `decisions/log.md` — AD-001 explains why MIT is the default recommendation
- `hub/launch.md` — Pre-launch checklist
