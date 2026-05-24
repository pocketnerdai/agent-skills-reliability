---
name: on-call-handoff
description: Run sustainable on-call rotations and clean shift handoffs so context survives, responders don't burn out, and nothing falls through the cracks.
when-to-use: Designing an on-call rotation, doing a shift handoff, reviewing on-call health/load, or when paging volume is hurting the team.
---

# On-Call & Handoff

On-call is a system you operate, not a punishment you assign. A healthy rotation makes incidents survivable; a broken one drives your best engineers out the door.

## Rotation design

- **Follow-the-sun if you can.** Paging someone at 3am their time is the most expensive way to get a slow, error-prone response. Distributed teams should cover their own daylight hours.
- **Sustainable load.** A widely-cited target: **no more than ~2 actionable pages per on-call shift.** Beyond that, responders can't both fight fires and fix root causes — and they burn out. If you're over, the fix is fewer/better alerts (see alert-design), not a tougher schedule.
- **Compensate on-call**, in pay or time off. Unpaid expectation-of-availability is how you lose people.
- **Primary + secondary.** Secondary is the backstop when primary misses a page or needs help. Define the escalation timeout explicitly (e.g. unacked page escalates to secondary in 5 min, then to manager).
- **Reasonable shift length** (a week is common) with handoffs at predictable times, never mid-crisis.
- **New responders shadow first.** They observe a rotation before holding the pager solo, and pair on their first real pages.

## The handoff

Every shift change is a chance to drop context. Make it a deliberate ritual, written + verbal:

- **Active incidents**: anything ongoing, with the channel/doc link and current state. The incoming responder must be able to take over cold.
- **Watch items**: "X has been flapping," "deploy of Y is risky, going out at 2pm," "vendor Z has a maintenance window tonight." Things that aren't on fire but could be.
- **Recently changed**: deploys, config, infra changes during the outgoing shift — the usual suspects if something breaks next.
- **Silenced/suppressed alerts**: anything muted and *why*, with an expiry. Silences that outlive their reason cause missed incidents.
- **Open follow-ups**: postmortem action items, tickets the incoming person should be aware of.
- Confirm the incoming responder's **pager/access actually works** before the outgoing one logs off. "Can you page yourself?" Test it.

## On-call health is an SRE metric

Track and review it like any other reliability signal:

- **Pages per shift**, especially overnight pages — overnight pages should be rare and always actionable.
- **% of pages that were actionable** vs. noise. Low actionability is an alert-design bug; fix it, don't tolerate it.
- **Time-to-ack** — slow acks mean alert fatigue or a broken paging path.
- **Repeat pages** — the same alert firing weekly is unfixed toil; file it for elimination.
- Review these in the team's regular reliability review. Trending the wrong way is a leading indicator of attrition.

## Anti-patterns

- Heroes who "just handle everything" — they hide systemic load and become a single point of failure.
- Silent handoffs (or none) — incoming responder rediscovers everything the hard way.
- Permanent alert silences with no expiry or owner.
- Pages that aren't actionable training responders to ignore the pager (the dangerous outcome).
- No secondary, no escalation path — a missed page becomes a missed incident.
