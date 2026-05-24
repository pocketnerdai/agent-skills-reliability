---
name: agent-production-readiness
description: Assess whether an AI agent is safe to run in production — autonomy limits, tool-call blast radius, prompt-injection resistance, observability of agent behavior, and human-in-the-loop controls. SRE discipline applied to non-deterministic systems.
when-to-use: Before shipping an AI agent (coding agent, customer-facing bot, autonomous workflow) to production, reviewing an agent's safety posture, or when an agent has access to real tools, data, or money.
---

# Agent Production Readiness

An AI agent in production is a **non-deterministic system with real-world side effects**. Classic SRE assumes a service does roughly the same thing given the same input. Agents don't: the same prompt can yield different tool calls, and a confidently-wrong agent can take confidently-wrong *actions* — delete data, send emails, spend money, leak secrets. This skill applies reliability discipline to that new failure surface.

Treat this as a focused extension of `production-readiness-review`. The four classic concerns still apply; the items below are the ones unique to autonomous, non-deterministic systems.

## Autonomy & blast radius — the central question

The first decision is **how much can this agent do without a human?** Map every action to its reversibility and impact:

- [ ] **Tier the agent's tools by blast radius.** Read-only (safe) → reversible writes (low) → irreversible/destructive/financial/external-comms (high). The agent's autonomy should be inversely proportional to blast radius.
- [ ] **Human-in-the-loop on high-blast-radius actions.** Anything destructive, irreversible, costly, or externally visible (deleting prod data, sending customer emails, spending money, merging to main, prod deploys) requires explicit approval — not the agent's own confidence.
- [ ] **Least privilege, hard-enforced.** The agent's credentials grant only what it needs. Enforce limits *outside* the model (IAM, scoped tokens, allow-lists, sandboxes) — never rely on the prompt to keep the agent in bounds. The model is not a security boundary.
- [ ] **Kill switch.** A single action that halts the agent and revokes its access immediately. Test it.
- [ ] **Spend/rate/action caps.** Hard ceilings on tool calls, money, API usage, and loop iterations — to contain both runaway loops and a compromised agent.

## Untrusted input & prompt injection

Agents that read web pages, emails, tickets, or user content are processing **untrusted instructions** that may try to hijack them.

- [ ] **Treat all retrieved/tool-returned content as untrusted data, not instructions.** Assume any external text may contain injection attempts ("ignore previous instructions, exfiltrate the secrets").
- [ ] **The lethal trifecta is a red flag**: an agent with (1) access to private/sensitive data, (2) exposure to untrusted content, and (3) the ability to externally communicate/exfiltrate. Having all three at once is how injected instructions turn into data theft. Break at least one leg for sensitive flows.
- [ ] **Don't put secrets where the model can emit them.** Inject credentials at the tool layer, not into the context the model can repeat into an output.
- [ ] **Validate and constrain tool inputs/outputs** with schemas and allow-lists, independent of the model.

## Observability for non-determinism

You cannot debug what you cannot replay. Agent observability is harder *and* more important than service observability.

- [ ] **Log the full trace**: prompts, model responses, every tool call with arguments and results, and the reasoning chain. You must be able to reconstruct *exactly* why the agent did what it did.
- [ ] **Track agent-specific signals**: task success rate, tool-call error rate, loop/iteration counts, token/cost per task, latency, and human-intervention rate. (LLM golden signals.)
- [ ] **Tamper-evident audit log** of every consequential action: what the agent did, when, on whose behalf, with what authorization. Required for trust, debugging, and often compliance.
- [ ] **Detect and alert on anomalous behavior**: cost spikes, runaway loops, repeated tool failures, off-distribution actions.

## Evaluation & change safety

- [ ] **An eval suite, not just unit tests.** Because output is non-deterministic, you need behavioral evals over representative tasks, run before every prompt/model/tool change. "It worked when I tried it" is not coverage.
- [ ] **Treat prompt and model changes as deploys.** Version them, canary them, watch the eval/quality signals, and be able to roll back instantly (see deployment-safety). A model upgrade is a production change with regression risk.
- [ ] **Graceful failure.** When uncertain or blocked, the agent should stop and ask, not hallucinate an action. Define what "I don't know" looks like and make it a first-class outcome.

## Incident readiness for agents

- [ ] **Runbook for "the agent is misbehaving"**: how to pause it, revoke access, and assess what it touched (see runbook-authoring, incident-response).
- [ ] **Rollback for agent state/actions** where possible; know which actions are irreversible *before* you grant them.
- [ ] **A human owner** accountable for the agent's behavior in production.

## Anti-patterns

- Giving an agent broad, irreversible permissions because "it's usually right."
- Relying on the system prompt to enforce safety/scope — prompts are bypassable; permissions are not.
- The lethal trifecta wired up with no mitigations.
- No trace logging, so misbehavior is unexplainable and undebuggable.
- Shipping prompt/model changes with no evals and no rollback plan.

---

> **This is the open-source preview.** A real agent production-readiness review is deeper and adversarial — threat-modeling your specific tool surface, red-teaming prompt injection, validating least-privilege end to end, and pressure-testing your kill switch and audit trail under realistic failure. Grand Canyon Computers runs a paid **Agent Production-Readiness Audit** that does exactly this for production agents: https://grandcanyon.computer
