---
name: production-readiness-review
description: Gate a service into production (or its first real traffic) with a structured PRR checklist covering reliability, observability, operability, and security basics.
when-to-use: Before launching a new service, taking on a service for on-call ownership, a major architecture change, or any "are we actually ready for prod?" decision.
---

# Production Readiness Review (PRR)

A PRR is the gate between "it works on my machine / in staging" and "real users and an on-call pager depend on it." Run it *before* launch, when there's still time to fix gaps cheaply. Skipping it just moves the discovery of every gap into a 3am page.

Walk the checklist below. Each item is **owned, evidenced, and either done or has a tracked exception with a deadline** — not "we'll get to it."

## Reliability & resilience

- [ ] **SLOs defined** with SLIs you can actually measure, and an error-budget policy (see slo-error-budgets).
- [ ] **Dependencies mapped**, each with a defined failure behavior — timeout, retry-with-backoff-and-jitter, circuit breaker, fallback. No unbounded waits.
- [ ] **No untracked single points of failure**; redundancy across instances/AZs appropriate to the SLO.
- [ ] **Graceful degradation**: under partial failure or overload, it sheds load / serves reduced function rather than collapsing.
- [ ] **Capacity validated** via load test to the SLO knee; autoscaling bounds set; headroom for spike + one failure (see capacity-planning).
- [ ] **Resilience tested**, ideally a game day: dependency loss, instance kill, overload (see chaos-gameday).

## Observability

- [ ] **Four golden signals** instrumented — latency (success vs. error, percentiles), traffic, errors, saturation (see observability-instrumentation).
- [ ] **Structured logs** with a propagated request/trace ID; **distributed tracing** for multi-service paths.
- [ ] **Dashboards** that answer "is the user OK?" and "what changed?", with deploy/version markers.
- [ ] You can compute the SLO from telemetry. If not, instrumentation is incomplete.

## Alerting & on-call

- [ ] **Symptom-based alerts** that page only on actionable, user-facing problems (see alert-design).
- [ ] Every alert links a **runbook** with exact, tested steps (see runbook-authoring).
- [ ] **On-call rotation** staffed, with escalation path and a trained owner; the team accepting the pager has agreed to it (see on-call-handoff).

## Operability & deployment

- [ ] **Progressive delivery**: canary/blue-green/rolling with health gates (see deployment-safety).
- [ ] **Automated, tested rollback**; last known-good artifact retained.
- [ ] **DB migrations** are expand/contract, forward-only, backward-compatible.
- [ ] **Config and secrets** externalized (no secrets in code/images); rotation possible.
- [ ] **Backups exist and a restore has actually been tested** — an untested backup is a hope, not a backup.

## Security & data basics (SRE-adjacent, still gating)

- [ ] **Least-privilege** access for the service and its operators; no shared god-credentials.
- [ ] Secrets in a manager, never in source or logs; TLS for data in transit.
- [ ] Sensitive data handling and retention defined; PII not leaking into logs/telemetry.
- [ ] Known-critical dependency vulns triaged.

## Documentation & ownership

- [ ] **Clear owning team** and escalation contacts.
- [ ] Architecture/design doc, dependency list, and runbooks discoverable from the alert.
- [ ] A documented disaster-recovery / "how do we rebuild this" story for stateful systems.

## How to run it

- Treat unchecked items as **launch risks**: fix, or accept explicitly with an owner and a deadline. "We'll add monitoring later" is the classic regret — do it before traffic, not after the first blind outage.
- Right-size to risk: a customer-facing payments service gets the full gauntlet; an internal low-stakes tool can carry more exceptions — but *decide* deliberately, don't skip silently.
- The PRR is a conversation, not a rubber stamp. The goal is shared understanding of what's solid and what's a known gap.

> **Going deeper.** A PRR for a *human-operated* service is well-trodden. Production-readiness for **AI agents** acting in production — tool-call blast radius, prompt-injection and untrusted input, non-determinism, autonomy limits, audit trails, kill switches — is a newer and sharper-edged version of this. See the `agent-production-readiness` skill.

## Anti-patterns

- Launching first, adding observability/alerting/runbooks "later" (i.e. after the first blind outage).
- A PRR that's a checkbox ritual nobody can produce evidence for.
- Backups that have never been restore-tested.
- Accepting on-call for a service nobody on the team understands or owns.
