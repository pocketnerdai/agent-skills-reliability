---
name: alert-design
description: Design alerts that page on user-facing symptoms, are always actionable, and don't drown on-call in noise. Symptom-based, not cause-based.
when-to-use: Creating or reviewing alerts/monitors, deciding what should page vs. ticket, tuning thresholds, or fighting alert fatigue and pager noise.
---

# Alert Design

Every page wakes a human. That human is expensive, fallible, and easily trained — by noise — to ignore the pager. So the bar is brutal: **a page must be urgent, important, actionable, and real.** If it's not all four, it shouldn't page.

## Alert on symptoms, not causes — the core principle

Alert on **what the user experiences**, not on every internal mechanism that might cause it. This is the single most important alerting decision.

- **Symptom-based** (good): "checkout error rate > 2%", "p99 latency > 1s", "the SLO is burning too fast." These page only when users are actually hurting.
- **Cause-based** (mostly bad): "CPU > 90%", "a pod restarted", "disk 80% full", "replica count dropped." These fire constantly without user impact and miss novel failures that don't match any predicted cause.

Why symptoms win:
- **Fewer alerts, higher signal.** One symptom alert ("users can't check out") replaces dozens of cause alerts, each a potential false page.
- **Catches unknown-unknowns.** You can't enumerate every cause; you *can* detect that users are unhappy however it happened.
- High CPU with the user perfectly happy is **not an incident** — it's information. Don't page for it.

Causes still belong on **dashboards and as diagnostic context** — they tell you *why* once a symptom alert fires. They just shouldn't wake anyone on their own.

## Page vs. ticket vs. log

Three tiers, used deliberately:

- **Page** (wake a human now): active or imminent user-facing harm that needs a response within minutes. Must be actionable *right now*.
- **Ticket** (look at it during business hours): degradation or risk that matters but can wait — disk filling slowly, a retry budget creeping up.
- **Log/dashboard** (no notification): context for investigation. The default home for cause-level signals.

Most things teams page on belong in the bottom two tiers.

## Every page must be actionable

- If the responder's only move is "acknowledge and go back to sleep," it should not have paged. Demote it.
- Every alert links a **runbook** with the response steps (see runbook-authoring). A page with no runbook is a riddle at 3am.
- The alert message states **impact in user terms** and where to start looking — not just "metric X crossed threshold Y."

## Tuning to kill noise

- **SLO burn-rate alerting** beats static thresholds for the big stuff. Multi-window, multi-burn-rate: a *fast burn* (e.g. 14.4x budget over 1h) pages immediately; a *slow burn* (e.g. 3x over 6h) tickets. This catches both sudden outages and slow bleeds while resisting flapping.
- Set thresholds off real baselines, with **hysteresis / minimum duration** so a 5-second blip doesn't page. Alert on sustained badness.
- **Track alert quality** like a product metric: % actionable, false-positive rate, pages per shift (see on-call-handoff). Routinely prune the noisiest alerts — a quarterly "alert review" that deletes/demotes the worst offenders is one of the highest-ROI reliability rituals.
- Avoid alerting on every dependency separately — you'll get N pages for one incident. Alert on the user-facing symptom; let traces find the failing dependency.

## Anti-patterns

- Paging on CPU/memory/disk/restart-count with no user impact.
- An alert with no runbook and no stated impact.
- Static thresholds that flap, or that someone set once and never revisited.
- A flood of cause alerts for a single root failure, burying the one signal that mattered.
- Tolerating chronic noisy alerts "because someone might need it" — that's how the pager gets ignored when it's real.
