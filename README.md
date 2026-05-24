# Reliability & SRE Skills for AI Agents

A pack of structured, loadable **skills** that teach an AI coding agent to think and act like a senior Site Reliability Engineer — running incidents, designing alerts and SLOs, reviewing deployments, and judging whether a system (including an AI agent itself) is actually ready for production.

These aren't prompts full of platitudes. Each skill encodes the real, opinionated judgment an experienced SRE applies under pressure: mitigate before you diagnose, alert on symptoms not causes, postmortems are blameless or they're useless, 100% uptime is the wrong goal, and a backup you haven't restored isn't a backup.

## What this is

A skill is a Markdown file (`skills/<name>/SKILL.md`) with YAML frontmatter — `name`, `description`, and `when-to-use` — followed by expert guidance. An agent that supports skills (Claude Code and others in the emerging "skills for agents" ecosystem) loads the relevant skill when the situation matches its `when-to-use`, and then behaves accordingly. Think of it as giving your agent a senior SRE's instincts on demand.

## Who it's for

- **Engineers using AI coding agents** who want the agent to handle reliability work with real rigor instead of generic advice.
- **Platform / DevOps / SRE teams** standardizing how their agents (and humans) approach incidents, alerting, deploys, and readiness reviews.
- **Anyone shipping an AI agent to production** who needs the agent — or themselves — to reason about agent-specific failure modes (see `agent-production-readiness`).

## Install / use

Clone the pack somewhere your agent can read:

```bash
git clone https://github.com/grandcanyon-computers/agent-skills-reliability.git
```

Then point your agent at the `skills/` directory. With **Claude Code**, the common patterns are:

- Drop the skill folders into your project's or user-level skills directory so they're auto-discovered, **or**
- Reference a specific skill in your prompt, e.g. *"Use the incident-response skill — we have elevated 5xx on checkout."*

Each `SKILL.md` is self-contained and human-readable, so you can also just open the relevant one and follow it yourself. The skills cross-reference each other by name (e.g. alert-design points at runbook-authoring) so an agent can chain them naturally.

## The skills

| Skill | What it covers |
|---|---|
| [`incident-response`](skills/incident-response/SKILL.md) | Drive a live incident: IC roles, mitigate-first, one change at a time, comms cadence |
| [`slo-error-budgets`](skills/slo-error-budgets/SKILL.md) | SLIs/SLOs done right, error budgets as a ship-vs-freeze decision tool, burn rates |
| [`runbook-authoring`](skills/runbook-authoring/SKILL.md) | Runbooks an exhausted on-call (or agent) can execute at 3am and fail safe |
| [`postmortem-blameless`](skills/postmortem-blameless/SKILL.md) | Blameless postmortems that find systemic causes and produce tracked action items |
| [`on-call-handoff`](skills/on-call-handoff/SKILL.md) | Sustainable rotations, clean shift handoffs, on-call as a measured SRE metric |
| [`dora-metrics`](skills/dora-metrics/SKILL.md) | The four DORA metrics as paired throughput + stability, never as a stick |
| [`observability-instrumentation`](skills/observability-instrumentation/SKILL.md) | Four golden signals, structured logs, traces, high-cardinality context, OTel |
| [`alert-design`](skills/alert-design/SKILL.md) | Symptom-based alerting, page vs. ticket vs. log, burn-rate alerts, killing noise |
| [`capacity-planning`](skills/capacity-planning/SKILL.md) | Forecasting, load testing to the knee, autoscaling limits, retry/thundering-herd failure modes |
| [`chaos-gameday`](skills/chaos-gameday/SKILL.md) | Hypothesis-driven chaos and game days with a controlled blast radius and abort switch |
| [`reliability-review`](skills/reliability-review/SKILL.md) | The recurring review that turns SLOs, incidents, on-call load, and toil into action |
| [`deployment-safety`](skills/deployment-safety/SKILL.md) | Canary/blue-green, automated rollback, feature flags, expand/contract DB migrations |
| [`resilience-patterns`](skills/resilience-patterns/SKILL.md) | Timeouts, retries with backoff+jitter, circuit breakers, bulkheads, graceful degradation |
| [`production-readiness-review`](skills/production-readiness-review/SKILL.md) | A PRR checklist gating a service into production across reliability, observability, ops, security |
| [`agent-production-readiness`](skills/agent-production-readiness/SKILL.md) | Production-readiness for **AI agents**: autonomy limits, blast radius, prompt injection, audit trails |

## A note on the paid audit

These skills are free and MIT-licensed — use them, fork them, ship them. If you're putting an **AI agent into production** and want an adversarial, expert review of its safety posture, Grand Canyon Computers offers a paid **Agent Production-Readiness Audit**: https://grandcanyon.computer

## Contributing

Contributions welcome. The bar for a skill is high: tight, specific, opinionated, and reflecting real operational judgment — not generic filler. If you add or edit a skill:

- Keep the frontmatter shape (`name`, `description`, `when-to-use`).
- Lead with the decision/principle, not background theory.
- Include an **Anti-patterns** section — naming what *not* to do is often the most useful part.
- Cross-reference related skills by name.

Open an issue to discuss a new skill before a large PR.

## License

MIT — see [LICENSE](LICENSE). Copyright Grand Canyon Computers.
