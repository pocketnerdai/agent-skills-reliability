---
name: slo-error-budgets
description: Define SLIs/SLOs and run an error budget so reliability becomes a number that drives decisions, not a vibe.
when-to-use: Setting reliability targets for a service, deciding "how reliable is reliable enough," arbitrating ship-vs-stabilize, or when someone proposes "100% uptime" or a number with no measurement behind it.
---

# SLOs & Error Budgets

Reliability is a feature with a cost. The job is to pick the **right** target — high enough that users are happy, low enough that you're not bankrupting velocity for nines nobody can perceive.

## The vocabulary, used correctly

- **SLI** (indicator): a measured ratio of good events to total events. E.g. `good = requests served < 300ms with 2xx/3xx; total = all valid requests`. SLIs are **ratios**, expressed 0–100%.
- **SLO** (objective): the target for an SLI over a window. E.g. "99.9% of requests succeed over a rolling 28 days."
- **SLA** (agreement): a contractual SLO with financial penalties. Your SLO should be **stricter** than your SLA — the gap is your safety margin.
- **Error budget**: `100% − SLO`. At 99.9% over 28 days that's ~40 minutes of allowed badness. Spend it however you like.

## Pick SLIs that reflect user happiness

Measure the user's experience, not the machine's. Good SLI types:

- **Availability**: fraction of valid requests that succeed.
- **Latency**: fraction of requests faster than a threshold. Always use a threshold + percentile, never an average. Track p99 (and p99.9 for critical paths) — averages hide the tail where real users suffer.
- **Quality/correctness**: fraction of responses that are correct/complete (e.g. search returned results, not an empty fallback).
- **Freshness/coverage** for data pipelines and batch.

Rules:
- **Measure as close to the user as possible** — load balancer or client beats deep-internal-service. Internal 200s mean nothing if the edge returned 500.
- Exclude invalid traffic (bot 404s, malformed requests) from the denominator, deliberately and documented.
- One service can have a few SLOs (availability + latency). Don't drown in dozens.

## Setting the number

- **Don't aim for 100%.** It's impossible (the user's network, DNS, and devices fail) and the marginal cost of each nine roughly 10x's. Past a point users can't tell 99.9% from 99.99%.
- Anchor to **what users actually need** and what you already deliver. If you've quietly been at 99.95%, "99.9%" is honest; "99.999%" is fantasy.
- More nines = real money: redundancy, on-call load, slower releases. Make leadership choose the cost knowingly.

## Running the error budget — this is the point

The budget converts reliability into a **decision-making tool**, ideally an automatic policy agreed in advance:

- **Budget remaining → ship.** You're reliable enough; spend the surplus on velocity, risky launches, experiments.
- **Budget exhausted → freeze.** Stop feature launches; redirect to reliability work until you're back in budget. This is the part teams skip — and then the SLO is just decoration.
- Track **burn rate**, not just remaining budget. Burning a month's budget in an hour is a page-now event; a slow steady drip is a ticket. Multi-window, multi-burn-rate alerts (e.g. fast: 14.4x over 1h; slow: 3x over 6h) catch both without flapping.
- Review SLOs quarterly. Chronic over-achievement → tighten or reallocate. Chronic misses → loosen to honest, or invest, but never quietly ignore.

## Anti-patterns

- "100% uptime" as a goal. It signals nobody has done the math.
- An SLO with no error-budget **policy** — nobody knows what happens when it's blown, so nothing happens.
- Averaging latency. p50 looks fine while p99 is on fire.
- Measuring success deep inside the system where the user's real failures are invisible.
- Counting bot/abuse traffic in the denominator and gaming the number.
