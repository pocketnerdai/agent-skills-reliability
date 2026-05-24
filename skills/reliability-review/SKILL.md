---
name: reliability-review
description: Run a recurring reliability review that turns SLOs, incidents, on-call load, and toil into prioritized action — the operational rhythm that keeps reliability from drifting.
when-to-use: Establishing or running a weekly/monthly reliability review or service health check, assessing a service's operational health holistically, or deciding where to invest reliability effort next.
---

# Reliability Review

Reliability decays silently between incidents. A recurring review is the forcing function that catches drift early and decides where the next unit of reliability effort goes. It's the operational counterpart to a sprint review — but for "is production healthy and is the team OK?"

## Cadence and inputs

Run it on a fixed cadence (weekly for a busy service, monthly for a stable one), short and data-driven. Pull from systems, not opinions:

- **SLO / error-budget status** — where's each SLO, and what's the burn rate? Budget exhausted → trigger the freeze policy (see slo-error-budgets). Chronically over-achieving → consider reallocating effort.
- **Incidents since last review** — count, severity, and crucially **MTTD vs. MTTR trends**. Are we detecting faster? Recovering faster? Same root cause recurring?
- **Postmortem action items** — completion rate. Stalled items mean a known cause is still live. This is where action items go to be held accountable, not to die.
- **On-call health** — pages per shift, overnight pages, % actionable, time-to-ack (see on-call-handoff). Trending up = leading indicator of both outages and attrition.
- **Toil load** — how much time went to repetitive manual ops vs. engineering. Rising toil is a reliability tax that compounds.
- **DORA trend** — delivery throughput and stability over time (see dora-metrics).
- **Risk register** — known weaknesses not yet incidents: single points of failure, expiring certs, capacity ceilings, brittle dependencies, aging runbooks.

## Make it produce decisions

The review is worthless if it's just a status read-out. Each meeting ends with **prioritized, owned actions**:

- **Reduce toil**: what repetitive work can we automate or eliminate this period? Toil reduction is real reliability work, not a side quest.
- **Fix top risks**: pick the one or two highest-leverage items from the risk register and fund them.
- **Tune alerts**: which alerts were noisiest? Demote or delete them (the quarterly alert prune lives here).
- **Right-size SLOs**: any objective that's consistently wrong gets corrected — to honest, or to a real investment plan.
- **Adjust on-call** if load is unsustainable: fewer alerts, more automation, schedule changes.

## The judgment calls

- **Toil vs. features.** A healthy team caps toil (a common target: under ~50% of time on toil) and protects engineering time to *reduce* it. If every cycle goes to keeping the lights on, reliability and morale both rot. Name the trade-off explicitly to leadership.
- **Where the next nine comes from.** Don't spread reliability effort thin. Find the *current* dominant source of unreliability (often one service, one dependency, one class of alert) and fix that — diminishing returns are everywhere else.
- **Lead vs. lag.** Incidents are lagging indicators. Watch leading ones — rising on-call load, growing toil, aging runbooks, creeping latency, shrinking error budget — and act before they become incidents.

## Anti-patterns

- A status meeting that produces no owned action items.
- Reviewing incidents but ignoring on-call load and toil (the human side of reliability).
- Letting postmortem action items quietly stall with nobody tracking completion.
- Spreading effort evenly instead of attacking the dominant failure source.
- Only looking at lagging indicators (incidents) and getting surprised by the next outage.
