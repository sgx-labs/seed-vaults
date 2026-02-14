---
title: "Launch Day — Hour-by-Hour Checklist"
tags: [launching, checklist, launch-day, operations]
content_type: research
domain: business
---

# Launch Day — Hour-by-Hour Checklist

## The Night Before

- [ ] Test payment flow one final time with a real card
- [ ] Test signup flow in incognito browser
- [ ] Check landing page on mobile
- [ ] Draft responses for common questions (save in a doc for quick copy-paste)
- [ ] Set up a monitoring dashboard (uptimerobot, analytics, error tracking)
- [ ] Queue social media posts (but do not auto-post — you want control)
- [ ] Get a good night's sleep (launch day is a marathon)

## Morning (6-9 AM Your Time)

- [ ] Check that the product is up and working
- [ ] Post on Product Hunt (if that is your primary channel)
- [ ] Post your maker's comment on PH immediately
- [ ] Send launch tweet/X post
- [ ] Email your waitlist: "We are live!"
- [ ] Post on Indie Hackers
- [ ] Personal messages to 10-20 supporters asking them to check it out
- [ ] Check error tracking for any issues

## Midday (9 AM - 12 PM)

- [ ] Respond to every comment on every platform (PH, HN, X, IH)
- [ ] Share a progress update: "2 hours in, X signups, thank you!"
- [ ] Fix any bugs reported by early users (prioritize signup/payment flow)
- [ ] Post in 1-2 relevant Slack or Discord communities
- [ ] Retweet/share positive feedback

## Afternoon (12-5 PM)

- [ ] Continue responding to comments
- [ ] Share another progress update
- [ ] Post on Reddit (if you have not yet)
- [ ] Thank specific supporters publicly
- [ ] If on PH: share your ranking update
- [ ] Handle any support emails that come in

## Evening (5-10 PM)

- [ ] Final round of comment responses
- [ ] Share end-of-day results: "Day 1 results: X signups, Y comments, Z feedback themes"
- [ ] Thank the community
- [ ] Note the top 3 feature requests for tomorrow
- [ ] Check analytics: where did traffic come from?
- [ ] Celebrate (seriously — you launched a product)

## Day After

- [ ] Send personalized follow-up email to every signup
- [ ] Compile all feedback into a prioritized list
- [ ] Fix the #1 reported issue
- [ ] Write a brief "lessons learned" thread on X
- [ ] Plan your next launch channel (if this was PH, plan for HN next week)
- [ ] Thank early customers directly

## What to Track on Launch Day

| Metric | Where to find it |
|--------|-----------------|
| Website visitors | Analytics (Plausible, Vercel) |
| Signup count | Database or auth dashboard |
| Payment conversions | Stripe dashboard |
| Top traffic sources | Analytics referrer data |
| Upvotes/points | PH or HN |
| Comments | All platforms |
| Error rate | Sentry or error tracking |

## Emergency Protocols

- **Site is down:** Fix immediately. Communicate on X: "Working on it, back in X minutes."
- **Payment is broken:** Disable payment temporarily, offer manual setup
- **Critical bug:** Deploy a fix. Transparency is better than pretending nothing happened.
- **Negative feedback:** Respond graciously. "Good point, we will look into that."
- **Nobody is signing up:** Check if the site actually loads. Check if PH approved your post.
