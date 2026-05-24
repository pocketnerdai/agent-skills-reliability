---
name: capacity-planning
description: Plan capacity from real demand and headroom, not guesswork — load testing, autoscaling, and the failure modes (retry storms, thundering herds) that make systems fall over under load.
when-to-use: Sizing infrastructure, preparing for a traffic spike/launch/seasonal peak, setting autoscaling, investigating saturation, or after an incident caused by overload.
---

# Capacity Planning

Capacity planning answers two questions: "will we fall over under expected load?" and "are we lighting money on fire on idle resources?" Both matter. The goal is enough headroom to absorb spikes and failures without paying for a fleet that's 90% idle.

## Plan from demand, not vibes

- **Forecast demand** from real signals: historical traffic with growth trend, seasonality (Black Friday, back-to-school, end-of-month batch), and known events (launches, marketing pushes, migrations). Distinguish **organic growth** (gradual, plan ahead) from **spikes** (sudden, must absorb instantly).
- **Translate demand to resources** via measured per-unit cost: "1000 req/s needs N cores / M GB / K connections," derived from load tests, not napkin math.
- **Build in headroom.** Run hot enough to be efficient, cool enough to survive a spike *plus* a failure. A common stance: size so you can lose a zone/AZ and still serve peak. Don't plan to run at 95% utilization — you have no room for the bad day, and most systems degrade well before 100%.

## Load test like production

- Test against **realistic traffic shapes**, not flat synthetic load — real traffic is bursty and mixed. Replay production patterns where you can.
- Find the **knee of the curve**: the point where latency degrades nonlinearly as load climbs. That's your real ceiling, not the point where it crashes. Capacity is "stays within SLO," not "doesn't die."
- Test **failure conditions**: what happens when a dependency is slow, a node dies, or a cache is cold. Capacity that only holds in perfect conditions is fake capacity.

## Autoscaling — useful, not magic

- Scale on the signal that **predicts** saturation for *your* workload — often a custom metric (queue depth, req/s, p99 latency) rather than CPU. CPU-based autoscaling reacts late for IO-bound or latency-sensitive services.
- **Scaling has lag.** New instances take time to boot, warm caches, and pass health checks. Autoscaling can't save you from an instantaneous spike — keep baseline headroom for the burst, let autoscaling handle the sustained ramp.
- Set sane **min/max bounds.** Min covers baseline + absorbs the spike before scaling catches up; max caps blast radius and cost (and protects you from a runaway-scaling bill during an attack or retry storm).
- Scale **down** carefully and with hysteresis — flapping (scale up, down, up) is its own incident and thrashes caches.

## The overload failure modes that bite

These turn a small problem into an outage; design against them:

- **Retry storms / retry amplification**: clients retrying a struggling service multiply load exactly when it can least handle it. Defend with **exponential backoff + jitter**, retry budgets (cap total retries), and **circuit breakers** that stop hammering a failing dependency.
- **Thundering herd**: many clients hitting the same resource simultaneously (cache expiry, restart, cron). Defend with jittered timers, request coalescing/single-flight, and staggered TTLs.
- **Cascading failure**: one saturated component pushes load onto others until everything tips. Defend with **load shedding** (reject excess early to protect the core), bulkheads (isolate resource pools), and graceful degradation (serve a cached/reduced response over none).
- **Cold-cache thundering herd after a deploy/restart**: warm caches or roll gradually so you don't hit the DB with full uncached load at once.

## Anti-patterns

- Sizing on average load and getting destroyed by peak.
- Treating autoscaling as a substitute for headroom against instantaneous spikes.
- Load testing with flat, unrealistic traffic and declaring victory.
- Naive retries with no backoff/jitter/budget — building a self-DDOS.
- Running at 90%+ utilization with no margin for the failure that *will* happen.
