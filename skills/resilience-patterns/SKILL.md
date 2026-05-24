---
name: resilience-patterns
description: Make a service survive its dependencies failing — timeouts, retries with backoff and jitter, circuit breakers, bulkheads, fallbacks, and graceful degradation.
when-to-use: Designing or reviewing how a service calls its dependencies, hardening a call path, diagnosing cascading failures or retry storms, or deciding what happens when a downstream is slow or down.
---

# Resilience Patterns

In any distributed system, **every network call will eventually fail, hang, or get slow.** Resilience is designing so that a dependency's bad day doesn't become your outage — and especially doesn't cascade into everyone else's. The default behavior of naive code (wait forever, retry blindly) is exactly what turns a small blip into a system-wide failure.

## Timeouts — the foundation

- **Every remote call has an explicit, finite timeout.** No exceptions. An unbounded call is a thread/connection held hostage; enough of them and your service exhausts its pool and dies waiting on something already dead.
- Set timeouts from real latency data (e.g. a bit above p99), not round numbers. Too tight = false failures; too loose = you hold resources through a hang.
- **Budget timeouts down the call chain.** If you owe the user a response in 2s and you call A then B, A and B can't each be allowed 2s. Propagate a deadline so inner calls fail fast instead of working on a request the caller already abandoned.

## Retries — necessary and dangerous

Retries fix transient blips and *cause* outages when done naively (the retry storm: everyone retrying a struggling service multiplies its load exactly when it's weakest).

- **Exponential backoff + jitter**, always. Backoff spaces retries out; jitter (randomization) prevents synchronized retry waves (thundering herd). Backoff without jitter just creates coordinated spikes.
- **Retry budget / cap.** Bound total retries (e.g. retries ≤ 10% of requests). When the budget's blown, stop retrying — the dependency is down, not blipping.
- **Only retry idempotent, retryable failures.** Retrying a non-idempotent write can double-charge or duplicate. Retrying a 400/permission error just wastes calls — it'll fail again.
- **Don't retry at every layer.** Retries stack multiplicatively (3 layers × 3 retries = 27 calls). Retry at one well-chosen layer.

## Circuit breakers — stop hitting what's down

- When a dependency's failure rate crosses a threshold, **open the circuit**: fail fast immediately instead of calling and waiting. This protects *you* (no resource exhaustion on a dead dependency) and *it* (no pile-on while it tries to recover).
- After a cooldown, go **half-open**: let a trickle through to test recovery; close on success, re-open on failure.
- A circuit breaker is what converts "downstream is down" from "we hang and die too" into "we fail fast and stay up (degraded)."

## Isolation & graceful degradation

- **Bulkheads**: isolate resource pools so one slow dependency can't consume all your threads/connections and starve unrelated work. Like watertight compartments — one floods, the ship stays up.
- **Fallbacks**: when a dependency is unavailable, serve a degraded-but-useful response — a cached value, a default, reduced functionality. A search box that returns slightly stale results beats a 500.
- **Load shedding**: under overload, reject excess requests early (cheaply) to protect the core from collapsing. Serving 90% of users well beats serving 100% a timeout.
- **Decide criticality per dependency.** A non-critical dependency (e.g. a recommendations widget) failing should never take down a critical path (e.g. checkout). Make non-critical calls best-effort with fast timeouts and fallbacks.

## Putting it together for one call

A well-hardened dependency call typically has: a deadline-aware **timeout**, **retry** with backoff+jitter for transient/idempotent failures within a budget, a **circuit breaker** to fail fast when it's truly down, a **fallback** for graceful degradation, and **bulkhead** isolation so it can't starve everything else. Plus instrumentation on each (see observability-instrumentation) so you can see it working.

## Anti-patterns

- Calls with no timeout — the original sin; one hung dependency exhausts your pool.
- Naive retries (no backoff, no jitter, no budget) — a self-inflicted DDOS on a struggling dependency.
- Retrying at every layer so failures amplify multiplicatively.
- A non-critical dependency on the critical path with no fallback, taking down core functionality.
- No circuit breaker, so you keep hammering (and hanging on) something that's already down.
