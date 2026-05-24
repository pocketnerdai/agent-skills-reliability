---
name: postmortem-blameless
description: Run a blameless postmortem that finds systemic causes and produces tracked, owned action items — not a blame ritual or a doc nobody reads.
when-to-use: After any incident worth learning from (any user-facing impact, any near-miss, any "we got lucky"). Use once the fire is out — during the fire, use incident-response.
---

# Blameless Postmortem

The purpose is **organizational learning**, not accountability theater. People act reasonably given what they knew, the tools they had, and the pressure they were under. If a competent engineer in the same position would have made the same call, the problem is the **system**, not the person.

## Why blameless is non-negotiable

Blame doesn't improve reliability — it teaches people to hide problems. The moment engineers fear punishment, you lose the honest detail you need to actually fix things, and your next incident gets reported late or not at all. **Psychological safety is a reliability control.** Protect it aggressively.

- Never write "the engineer should have known / failed to / was careless."
- Reframe to the system: not "Sam pushed a bad config" but "a config push reached prod with no validation gate and no canary, so a typo became a global outage."
- Assume good faith and competence. Ask "what made this action reasonable at the time?"

## Structure of a good postmortem

1. **Summary** — 3-4 sentences: what happened, user impact, duration, how it was resolved. Readable by an exec.
2. **Impact** — quantified: users affected, requests failed, revenue/SLO budget burned, duration. Numbers, not adjectives.
3. **Timeline** — UTC timestamps from detection to resolution. Include when it *started* vs. when you *detected* it (MTTD) vs. resolved (MTTR). These gaps are findings.
4. **Root cause(s)** — usually plural. Use the **Five Whys** but don't stop at the first human; keep going until you hit a process or system gap. The interesting answer is rarely the first one.
5. **What went well** — genuinely. Fast detection? A runbook that worked? Reinforce it.
6. **What went wrong / where we got lucky** — including near-misses. "It only didn't spread because traffic was low" is a finding.
7. **Action items** — the entire point.

## Action items are the deliverable

A postmortem with no tracked action items was a therapy session.

- Each item: **specific, owned by a named person, with a due date, tracked in your normal backlog** (not buried in the doc). If it's not in the tracker, it won't happen.
- Prefer items that **remove a class of failure**, not just patch this instance. "Add a validation gate to all config pushes" beats "fix this one typo."
- Be honest about priority. Ten P3 items nobody does is worse than two P1 items that ship. Right-size to what will actually get done.
- Avoid pure "be more careful" / "add more training" items — they don't change the system and they quietly assign blame.

## Process

- Write it within a few days, while memory is fresh. Decay is real.
- **Review it in a meeting**, blameless-facilitated, with the people involved and a few who weren't (fresh eyes, broader learning).
- Publish widely. The org learns from incidents it never experienced. A library of readable postmortems is a training asset.
- Track action-item completion in the following weeks. Stalled items = the cause is still live.

## Anti-patterns

- "Human error" as a root cause. It's never the root cause — it's a prompt to ask why the system let a human error cause an outage.
- Naming and shaming, even gently.
- Stopping the Five Whys at the first convenient answer.
- Action items with no owner, no date, no tracker — they evaporate.
- A polished doc that's filed and never read or acted on.
