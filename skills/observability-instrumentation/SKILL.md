---
name: observability-instrumentation
description: Instrument services so you can answer questions you didn't anticipate — the four golden signals, structured logs, traces, and high-cardinality context done right.
when-to-use: Adding or reviewing telemetry (metrics, logs, traces), deciding what to instrument, debugging "we can't tell what's wrong in prod," or setting up observability for a new service.
---

# Observability & Instrumentation

Monitoring tells you whether the things you predicted would break are broken. **Observability** lets you ask new questions about your system in production without shipping new code — to debug the failures you *didn't* predict. Modern distributed systems have too many failure modes to pre-define them all, so instrument for exploration.

## Start with the four golden signals

For any user-facing service, instrument these four before anything else (from Google's SRE practice):

1. **Latency** — time to serve a request. **Separate successful from failed latency** — a fast stream of errors will otherwise make latency look great. Track distributions/percentiles (p50/p99/p99.9), never just the average; the tail is where users hurt.
2. **Traffic** — demand on the system (req/s, transactions/s, concurrent sessions). Your denominator and your context for everything else.
3. **Errors** — rate of failed requests. Include the sneaky ones: 200s with wrong content, policy-violating slow successes.
4. **Saturation** — how full the most-constrained resource is (CPU, memory, IO, connection pool, queue depth). Most systems degrade before 100%, so target a utilization ceiling and alert before the cliff.

Cover these and you can characterize most service health. For resource-centric views, the **USE** method (Utilization, Saturation, Errors per resource) complements golden signals nicely.

## The three signals, used for their strengths

- **Metrics**: cheap, aggregatable, great for dashboards, trends, and SLOs. Low cardinality. Your "is something wrong?" layer.
- **Logs**: structured events with full context. Your "what exactly happened to *this* request?" layer.
- **Traces**: the path of a request across services with timing per hop. Your "where in the chain did it go wrong / get slow?" layer — essential in microservices.

Use them together: a metric/alert says *something's wrong*, a trace says *where*, logs say *what exactly*.

## Make telemetry actually useful

- **Structured logs, always** (JSON or key-value). A human-readable string you can't query is a diary, not telemetry. Machine-parseable beats pretty.
- **Propagate a correlation/trace ID** through every service and log line. Without it you cannot reconstruct one user's journey across services — the single highest-leverage thing in a distributed system.
- **High cardinality is a feature, not a sin.** Attach user ID, request ID, region, build/version, customer tier, endpoint as dimensions. The ability to slice by "errors for build v2.3 in eu-west for enterprise customers" is exactly what turns an unknown-unknown into an answer. (Mind metric cardinality cost — high-cardinality belongs on traces/logs/events, not on every metric label.)
- **Instrument at boundaries**: inbound requests, outbound calls to dependencies, queue publish/consume, cache hit/miss, DB queries. That's where failures and latency live.
- **Standardize on OpenTelemetry.** Vendor-neutral instrumentation so you can change backends without re-instrumenting. Don't hand-roll proprietary tracing.

## Connect telemetry to meaning

- Tie instrumentation to your **SLIs** — if you can't compute your SLO from your telemetry, you're missing instrumentation.
- Emit **deploy/version markers** so you can overlay "what changed" on every graph. Half of incident diagnosis is "it started at 14:02 — what deployed at 14:01?"
- Budget for cost: telemetry volume can rival infra cost. Sample traces intelligently (keep errors + slow requests), aggregate metrics, set log retention deliberately.

## Anti-patterns

- Averaging latency, or mixing success and error latency into one number.
- Unstructured string logs you can't query or aggregate.
- No request/trace ID — every cross-service debug becomes archaeology.
- Logging everything at the same level so real signal drowns; or logging so little that prod is a black box.
- Building dashboards full of metrics nobody can connect to "is the user OK?"
