---
name: incident-response
description: Drive a live production incident from detection to resolution with clear roles, comms, and a bias toward mitigation over root-cause.
when-to-use: An active or suspected production incident — elevated error rates, latency, partial/full outage, data integrity concern, or a page that just fired. Use the moment something is "on fire," not afterward (that's postmortem-blameless).
---

# Incident Response

You are the incident commander (IC) until you explicitly hand off. Your job is **coordination and decisions**, not debugging. The person typing fixes is rarely the right person to run the incident.

## First 5 minutes

1. **Declare.** If you're asking "is this an incident?", it's an incident. Declaring is cheap; a silent slow-burn outage is expensive. Open a dedicated channel (e.g. `#inc-2026-05-24-checkout`) and a timestamped doc.
2. **Assign roles.** Even solo, name them out loud:
   - **IC** — owns the incident, makes calls, holds the timeline. Does not fix.
   - **Ops/Tech lead** — drives the technical investigation.
   - **Comms** — updates stakeholders/status page on a fixed cadence.
   - **Scribe** — timestamps every significant action and decision.
3. **State the impact in one sentence**, in user terms: "Checkout is failing for ~30% of US users since 14:02 UTC." Not "the `orders` pod is CrashLooping."
4. **Set a sev level** and a comms cadence (Sev1 → update every 15 min even if "no change").

## Mitigate first, diagnose second

The goal is to **stop user pain**, not to understand the bug. These are different activities and mitigation wins.

- Ask: "What changed?" Deploys, feature flags, config pushes, infra changes, traffic shifts, expiring certs/credentials, upstream dependency status. Most incidents trace to a recent change.
- Reach for the **fastest safe lever**: roll back the last deploy, flip the feature flag off, fail over, shed load, scale out, drain a bad node. A rollback you can do in 90 seconds beats a forward-fix you "think" will work in 20 minutes.
- Don't fix forward under pressure unless rollback is impossible (e.g. a destructive schema migration already ran). Forward-fixes under stress create second incidents.

## Running the incident

- **One change at a time, announced before you make it.** "I'm rolling back orders to v4.21.0 now." Parallel uncoordinated changes destroy your ability to know what worked.
- Keep a **live timeline**: every hypothesis, action, and result with a UTC timestamp. This is your postmortem's raw material and it prevents re-treading.
- **Rotate responders** before they burn out. A 6-hour incident needs fresh eyes; tired IC = bad decisions.
- Distinguish **MTTD** (detect) from **MTTR** (resolve). If you only learned about this from a customer tweet, that's a detection gap — note it for the postmortem.
- Resist the "just one more thing" trap once mitigated. **Stabilize, then investigate.**

## Communicating

- Internal updates: what's impacted, what you're doing, ETA-to-next-update (not ETA-to-fix — you don't know that yet).
- External/status page: honest and boring. "We're investigating elevated errors in checkout." Never speculate on cause publicly.
- Loop in stakeholders proactively for Sev1/Sev2. Leadership finding out from Twitter is its own incident.

## Closing

- Declare resolved only when user-facing impact is gone **and** verified by the signal that detected it (not by vibes).
- Downgrade severity as impact shrinks; keep the channel open briefly to catch recurrence.
- Schedule the blameless postmortem **before everyone disperses**. Owner + due date, while the timeline is fresh.

## Anti-patterns

- Hero debugging in silence — no IC, no timeline, no comms.
- Treating root-cause analysis as the priority during active user impact.
- Making three changes at once and not knowing which one helped.
- Closing the incident without a scheduled postmortem and clear action-item owners.
