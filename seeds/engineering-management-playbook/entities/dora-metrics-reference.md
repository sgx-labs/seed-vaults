---
title: "DORA Metrics Reference"
tags: [DORA, metrics, deployment-frequency, lead-time, change-failure-rate, MTTR, DevOps]
content_type: entity
domain: engineering-management
---

# DORA Metrics Reference

## The Four Key Metrics

DORA (DevOps Research and Assessment) metrics are the most validated measures of software delivery performance, based on 7+ years of research across thousands of organizations.

### 1. Deployment Frequency

How often your team deploys code to production.

| Performance Level | Benchmark |
|-------------------|-----------|
| Elite | On-demand (multiple deploys per day) |
| High | Between once per week and once per month |
| Medium | Between once per month and once every 6 months |
| Low | Fewer than once every 6 months |

**How to measure**: Count production deployments per week/month. Exclude configuration changes and non-code deployments.

### 2. Lead Time for Changes

Time from code commit to that code running successfully in production.

| Performance Level | Benchmark |
|-------------------|-----------|
| Elite | Less than one hour |
| High | Between one day and one week |
| Medium | Between one week and one month |
| Low | Between one month and six months |

**How to measure**: Track the timestamp of the first commit in a changeset to the timestamp of its production deployment. Median, not average (outliers skew averages).

### 3. Change Failure Rate

Percentage of deployments that cause a failure requiring remediation (rollback, hotfix, patch).

| Performance Level | Benchmark |
|-------------------|-----------|
| Elite | 0-15% |
| High | 16-30% |
| Medium | 16-30% |
| Low | 46-60% |

**How to measure**: Count deployments that triggered an incident or required a rollback, divided by total deployments.

### 4. Mean Time to Recovery (MTTR)

How long it takes to restore service when an incident occurs.

| Performance Level | Benchmark |
|-------------------|-----------|
| Elite | Less than one hour |
| High | Less than one day |
| Medium | Between one day and one week |
| Low | More than one week |

**How to measure**: Time from incident detection to service restoration. Track across all severity levels, report separately.

## Key Insight

**Speed and stability are NOT tradeoffs.** Elite teams score highest on ALL four metrics simultaneously. Fast teams are also the most stable teams.

## Common Measurement Pitfalls

- **Gaming**: If DORA metrics become targets, teams will game them (tiny deploys to boost frequency, under-reporting failures)
- **Comparing across teams**: Different domains have different baselines. Compare a team against its own history, not against other teams.
- **Ignoring context**: A low deployment frequency might be appropriate for firmware or embedded systems
- **Measuring without acting**: Dashboards that nobody looks at or acts on are waste

## Tools for Tracking

- **Sleuth, LinearB, Jellyfish**: Commercial DORA tracking
- **GitHub/GitLab built-in metrics**: Basic deployment and PR metrics
- **Custom dashboards**: Prometheus + Grafana, or your CI/CD pipeline's data
- **Four Keys project**: Google's open-source DORA measurement tool

## See Also

- `hub/engineering-metrics.md` -- Broader metrics context
- `hub/incident-management.md` -- MTTR tracking
- `hub/team-health-indicators.md` -- DORA as a health signal
