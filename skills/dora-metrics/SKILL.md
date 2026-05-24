---
name: dora-metrics
description: Measure delivery performance with the four DORA metrics correctly — as a balanced pair of throughput and stability, never as a stick to beat teams with.
when-to-use: Assessing engineering delivery/reliability health, setting up DORA measurement, interpreting deploy-frequency/lead-time/CFR/MTTR, or pushing back on metric misuse.
---

# DORA Metrics

Four metrics, from years of DevOps research, that correlate with both org performance and engineer well-being. They come in **two pairs that must be read together** — throughput tells you how fast, stability tells you whether fast is safe.

## The four

**Throughput**
1. **Deployment Frequency** — how often you ship to production. Elite teams deploy on-demand, multiple times per day. High frequency means small batches, which means smaller blast radius per change.
2. **Lead Time for Changes** — commit → running in production. Elite is under an hour. Long lead times mean big risky batches and slow recovery from anything.

**Stability**
3. **Change Failure Rate (CFR)** — % of deployments causing a failure needing remediation (rollback, hotfix, patch). Elite is 0–15%. Read it *against* deploy frequency: a 5% CFR at 50 deploys/day is far healthier than 5% at one deploy/month.
4. **Failed Deployment Recovery Time** (formerly MTTR) — how fast you restore service after a failed change. Elite is under an hour. This is your resilience: things break; how fast do you recover?

## How to read them — the whole point

- **Throughput and stability are not a trade-off.** The central, counterintuitive DORA finding: elite teams are better at *both* simultaneously. Small frequent changes are *easier* to verify and recover from. If someone says "we have to choose between speed and reliability," they're describing a process problem, not a law of nature.
- **Never optimize one in isolation.** Pushing deploy frequency while ignoring CFR just ships more breakage faster. Always look at the pair.
- They're **team-level health indicators, not individual performance scores.** The instant they become a target on a dashboard ranking people, they get gamed (split commits to pad frequency, mislabel failures to lower CFR) and become worthless. Goodhart's law is undefeated.
- Use them to find **bottlenecks and trends over time**, not to compare teams against each other — context differs too much (a payments service vs. an internal tool legitimately deploy differently).

## What each metric points you toward

- **Low deploy frequency / long lead time** → look at batch size, manual gates, slow CI, painful release process, fear of deploying. Usually a pipeline and automation problem.
- **High CFR** → weak testing, no canary/staged rollout, poor pre-prod parity, missing automated checks. See deployment-safety.
- **Slow recovery** → no fast rollback, weak observability (can't find the problem), missing runbooks, unclear ownership. See incident-response and observability.

## Practical advice

- Measure them automatically from your CI/CD and incident systems. Hand-collected DORA metrics rot immediately.
- Watch the **trend**, quarter over quarter, for *your* team. Direction matters more than the absolute number.
- Pair DORA with **reliability** signals (SLOs, error budget) and **operational** signals (on-call load). DORA shows delivery; it doesn't show whether prod is actually healthy or whether the team is drowning.

## Anti-patterns

- Ranking individuals or teams on a DORA leaderboard.
- Celebrating deploy frequency while CFR quietly climbs.
- Treating "elite" thresholds as mandatory for every service regardless of risk profile.
- Collecting the numbers and never acting on what they point to.
