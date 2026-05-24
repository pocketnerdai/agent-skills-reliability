---
name: deployment-safety
description: Ship to production safely with progressive delivery — canary/staged rollout, automated rollback, feature flags, and forward-only DB migrations.
when-to-use: Designing or reviewing a deployment/release process, planning a risky rollout, setting up canary or feature flags, or after a deploy caused an incident.
---

# Deployment Safety

Most incidents are caused by a change. So the highest-leverage reliability investment is usually **how you deploy**, not how you build. The goal: make every release low-risk, observable, and trivially reversible — so deploying is boring.

## Progressive delivery — never big-bang to 100%

Roll changes out gradually and watch real signals at each step:

- **Canary**: send the new version a small slice of real production traffic (1% → 5% → 25% → 100%), comparing the canary's golden signals (error rate, latency, saturation) against the stable baseline at each stage. Promote only if healthy; halt and roll back on regression. A canary turns "we broke everyone" into "we briefly degraded 1%."
- **Blue/green**: stand up the new version (green) alongside old (blue), shift traffic over, keep blue warm for instant rollback. Fast cutover and fast revert, at the cost of running two fleets briefly.
- **Rolling**: replace instances incrementally with health gates. Cheap; slower to fully revert than blue/green.

In all cases: **deploy ≠ release.** Decouple them with feature flags so code can ship dark and be turned on independently, for a subset, and turned *off* instantly without a redeploy.

## Make rollback the default reflex

- **Roll back, don't roll forward, under pressure.** Reverting to a known-good version is faster and safer than authoring a fix while users hurt. Forward-fixes under stress cause second incidents (see incident-response).
- **Automate rollback.** Wire health checks / canary analysis to auto-halt and auto-revert on SLO regression. A rollback that needs a human to notice, decide, and execute is too slow.
- **Test that rollback actually works** — in a game day, not during an incident. A rollback path nobody has exercised is a guess.
- Keep the **last known-good artifact** immediately deployable. Never delete the version you might need in 30 seconds.

## Feature flags, used with discipline

- Decouple deploy from release; enable instant kill-switch; ramp gradually; target subsets (internal → beta → %).
- **Flags are debt.** Each one is a branch in your code and your testing matrix. Remove stale flags aggressively — a flag left on "temporarily" for a year is a latent landmine. Track flag lifecycle with owners and expiry.

## Database migrations — the dangerous part

DB changes can't "roll back" like code; a dropped column or rewritten data is gone. Use **expand/contract (parallel change)**:

1. **Expand**: add the new schema additively (new column/table), backward-compatible. Old code still works.
2. **Migrate/dual-write**: backfill and have code write both old and new.
3. **Contract**: only after the new path is proven and no code reads the old, remove the old schema — as a *separate, later* deploy.

Rules: migrations are **forward-only and backward-compatible** at each step; never couple a destructive schema change to the same deploy that introduces dependence on it; backfill large tables in batches to avoid lock/replication storms.

## Guardrails that make it boring

- Automated tests + health checks gate every promotion; a failing gate blocks the rollout, no override-by-default.
- Deploy in **small batches, frequently** — small changes are easier to verify, attribute, and revert (this is also what drives healthy DORA metrics).
- Emit a **deploy/version marker** to your telemetry so every dashboard shows "what changed when" (see observability-instrumentation).
- Avoid risky deploys right before nobody's watching — Friday-5pm and pre-holiday freezes exist for a reason; weigh urgency vs. who's around to respond.

## Anti-patterns

- Big-bang deploy straight to 100% with no canary.
- Rollback that's manual, untested, or impossible because the old artifact is gone.
- Coupling a destructive, irreversible DB migration to a feature deploy.
- Feature flags that accumulate forever with no owner or expiry.
- Forward-fixing under incident pressure when a clean rollback was available.
