---
name: runbook-authoring
description: Write runbooks an exhausted on-call engineer (or an agent) can execute at 3am without prior context, and that fail safe.
when-to-use: Creating or reviewing operational runbooks/playbooks, documenting a recovery procedure, attaching response steps to an alert, or turning tribal knowledge into something executable.
---

# Runbook Authoring

A runbook is for the worst-case reader: paged at 3am, half-awake, never seen this service, adrenaline high. If they have to think creatively, the runbook failed. Write for execution, not education.

## What every runbook needs

1. **Title = the alert/symptom**, verbatim. The reader arrives from a page that says `HighCheckoutErrorRate`; the runbook is named exactly that so it's found in 5 seconds.
2. **Severity & impact**: who's affected and how badly. Sets urgency.
3. **Prerequisites**: access, tools, VPN, roles needed — listed up front so the reader doesn't discover mid-incident they lack permissions.
4. **Triage / verify the alert is real**: the first step is *always* "confirm this is actually happening." Where to look, which dashboard (link it), what "normal" looks like. Kill false positives before acting.
5. **Diagnosis decision tree**: "If X, go to A; if Y, go to B." Branch on observable signals, not guesses.
6. **Resolution steps**: exact, copy-pasteable commands with real flags. Not "restart the service" — the literal command, the namespace, the expected output.
7. **Verification**: how to confirm the fix worked, using the same signal that fired the alert.
8. **Escalation**: who/what to page if these steps don't resolve it, with a clear time bound ("if not resolved in 15 min, page the database on-call").
9. **Rollback / undo**: how to reverse any step that made things worse.

## Make steps executable

- **Exact commands, copy-pasteable.** Show expected output so the reader knows if it worked. Parameterize clearly: `kubectl -n <namespace> rollout restart deploy/<service>`.
- **State the blast radius before destructive actions.** "This drops all active connections — expect a 10s blip." The reader must know the cost before pressing enter.
- **Fail safe.** Every dangerous step gets a guardrail: a dry-run first, a confirmation, an explicit "do NOT run this if the replica is primary."
- **Link, don't inline, the volatile stuff.** Dashboards, query consoles, related runbooks — link them. Don't paste a dashboard's contents that'll be stale next week.

## Keep them alive

- A runbook is **only as good as its last test.** Untested runbooks are fiction. Validate them during game days and real incidents; if a step was wrong, fix it before you close the incident.
- Date them and assign an owner. Add a "last verified" date — readers should distrust a runbook last touched 18 months ago.
- After every incident, the postmortem should ask: "Did the runbook exist? Was it right? Did it help?" Then fix it.
- Prune ruthlessly. A runbook for a decommissioned system is a trap.

## Style

- Imperative voice, numbered steps, one action per step.
- No background essays. The reader doesn't want to understand the architecture; they want the system healthy. Put theory in a linked design doc.
- Assume zero context about *this* service but competence with the *tools*.

## Anti-patterns

- "Restart the service and see if that helps" — vague, no command, no verification.
- Runbooks that explain *why* the system works instead of *what to do now*.
- Steps that require judgment the 3am reader doesn't have ("tune the parameters appropriately").
- Destructive commands with no stated blast radius or undo.
- A beautiful runbook nobody has ever executed.
