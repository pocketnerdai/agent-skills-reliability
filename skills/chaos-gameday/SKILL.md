---
name: chaos-gameday
description: Run chaos experiments and game days to find weaknesses before they find you — hypothesis-driven, blast-radius-controlled, with a real abort switch.
when-to-use: Planning or running a game day or chaos experiment, validating resilience/failover/runbooks, or building confidence that a system survives the failures you designed it to survive.
---

# Chaos Engineering & Game Days

Chaos engineering is the disciplined practice of **injecting failure on purpose to learn how the system really behaves** — because the difference between how you *think* it fails and how it *actually* fails is where outages live. It is an experiment with a hypothesis, not random breakage. "Breaking things to look cool" is not chaos engineering.

## The scientific method, applied to failure

1. **Define steady state** — a measurable signal of "healthy" (your SLIs: error rate, p99 latency, throughput). You must know normal before you perturb it.
2. **Form a hypothesis** — "If we kill one of three checkout pods, error rate stays under 0.1% and p99 stays under 1s because the load balancer reroutes." State the expected outcome *before* running. The experiment confirms or refutes it.
3. **Inject a real-world failure** — instance termination, AZ/zone loss, dependency latency or errors, packet loss, DNS failure, disk full, clock skew, expired cert. Pick failures that actually happen to you.
4. **Measure & compare** to steady state.
5. **If the hypothesis breaks, you found a bug before your users did** — that's a win. Fix it, then re-run to confirm.

## Control the blast radius — non-negotiable

- **Start tiny.** First experiment in staging, or on a single instance, or 1% of traffic. Expand only as confidence grows. The goal is to learn with minimal real harm.
- **Always have a stop button.** A one-action abort that halts the experiment and restores normal *immediately*. If you can't abort instantly, you're not ready to run.
- **Define abort criteria up front**: "if error rate exceeds X or real customers are impacted beyond Y, abort." Decide the line before adrenaline is involved.
- **Minimize blast radius**, then increase deliberately. Chaos in prod is powerful and legitimate — but earned, after staging proves the basics and after you trust your guardrails.
- **Announce experiments** (at least to on-call and stakeholders). A surprise that pages the unaware team and triggers a real incident response destroys trust. Game days are scheduled, communicated events.

## Game days vs. automated chaos

- **Game day**: a scheduled, often manual exercise where the team injects a failure (or simulates one) and **practices the human response** — runbooks, comms, escalation, the incident process itself. Half the value is testing *people and process*, not just the system. Treat it like a fire drill: real procedures, real timers, then a blameless debrief.
- **Automated/continuous chaos**: tooling that injects failures regularly (e.g. randomly terminating instances) so resilience stays a continuous property, not a one-time audit. Reserve for systems mature enough to absorb it safely.

## What to learn from each run

- Did the system stay in steady state? If not, where exactly did it break, and was the break graceful (shed load, degrade) or catastrophic (cascade)?
- Did **detection** work — did the right alert fire, fast? A failure that nobody noticed is a monitoring gap.
- Did the **runbook** match reality? Game days are the best runbook test you'll get.
- Did the **humans** respond well — clear IC, good comms, smooth handoff? Debrief blamelessly.
- Every finding becomes a tracked action item, same as a postmortem.

## Anti-patterns

- Injecting failure with no hypothesis and no steady-state baseline — you learn nothing measurable.
- No abort switch, or an abort that takes minutes — that's how an experiment becomes a real outage.
- Surprise chaos that pages an unaware on-call and burns trust.
- Jumping straight to prod / large blast radius before staging and guardrails are proven.
- Running the game day and never converting findings into fixes.
