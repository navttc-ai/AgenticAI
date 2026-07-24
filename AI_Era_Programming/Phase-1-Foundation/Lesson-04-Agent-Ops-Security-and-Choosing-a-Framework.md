# Lesson 10: Agent Operations, Security, and Choosing a Framework

## Purpose

Architecture on paper isn't enough — agents need to be operated, trusted across systems, and built on the right foundation. This lesson covers how you monitor and improve a running agent, how agents discover and trust each other safely, how to pick an SDK, and how to sketch your first agent concept before writing code.

## Guiding Questions

1. What does "Agent Ops" actually involve, beyond just watching an agent run?
2. What problem does the A2A protocol solve that MCP doesn't?
3. What questions should drive your choice of agent SDK or runtime?
4. What does the Agent Triangle say every effective agent concept needs?

---

## Agent Ops: Operating a Running Agent

### The Idea
Agent Ops means treating a deployed agent the way you'd treat any production system that needs ongoing care: tracing every run (via OpenTelemetry), sampling and scoring real traces to catch drift, and feeding the worst or most interesting ones back into your eval suite so tomorrow's tests are grounded in what actually happened in production — not just what you imagined might happen.

### In Simple Words
An agent that "worked in testing" isn't done — it's just started. Agent Ops is the ongoing discipline of watching what it actually does once real users are talking to it: catching when its answers start drifting worse, and turning yesterday's mistake into today's test case so the same failure can't sneak past unnoticed twice.

### Real Example
Agent Factory's own production observability pipeline runs in three layers with a clear division of labor:
- **OpenTelemetry (OTel)** traces one request across every service it touches — the model call, three tool calls, four database queries — recording *that* the model was called, not *why*.
- **The agent SDK's own trace** owns the agent-behavior view: which model decisions were made, which tools were called with what arguments, where handoffs went.
- **A trace-review tool (like Phoenix)** watches those traces over time, scores them, and flags the worst for promotion into the eval suite — turning bad production runs into future regression tests.

The operational rhythm: sample traces where the agent errored, where a user gave negative feedback, plus a small random sample for baseline coverage — then run a weekly "promotion ritual" where a human reviews sampled traces and decides which become new golden-dataset examples.

### The Pitfall
Treating observability as "logs you check when something breaks." Agent Factory's framing is proactive: a scheduled drift check (comparing a trailing 7-day average against a 30-day baseline) catches a metric quietly degrading *before* it becomes an incident anyone notices from a complaint.

### Guiding Question (Answer This From Memory Later)
What does "Agent Ops" actually involve, beyond just watching an agent run?

---

## Agent Interoperability & Security

### The Idea
MCP connects an agent to *tools*; A2A (Agent-to-Agent protocol) connects an agent to *other agents* — letting them discover each other, communicate, and delegate tasks directly. They solve different problems and are often used together in the same system.

### In Simple Words
MCP is plugging a device into a power outlet — your agent reaching out to a tool it needs. A2A is coworkers coordinating with each other — one agent finding another agent that offers a capability it needs, and handing off work to it directly, agent to agent, no human relay in between.

### Real Example
The practical decision isn't "MCP vs. A2A" — it's "where do my agent's services actually live?"
- Services internal to your own organization → **MCP**
- A network of partner agents you need to discover and delegate to → **A2A**
- Third-party APIs discovered at runtime → agent directories (a "Yellow Pages" for agent commerce)

These aren't mutually exclusive — a real production agent often uses several at once. In a multi-agent marketplace scenario, Agent B might publish its capability over A2A so Agent A can discover it, while each agent separately uses MCP internally to reach its own tools.

### The Pitfall
Assuming A2A replaces MCP, or vice versa. They operate at different layers of the same system — an agent that only ever needs its own tools has no reason to add A2A; an agent that needs to delegate work to a peer agent it's never talked to before has no way to do that through MCP alone.

### Guiding Question (Answer This From Memory Later)
What problem does the A2A protocol solve that MCP doesn't?

---

## The Agent SDK Landscape: Picking Your Engine

### The Idea
No single agent SDK or runtime is universally "best" — the right choice depends on your job profile: how much failure you can tolerate, how much operational burden you're willing to own, and how much vendor lock-in you're comfortable with.

### In Simple Words
Choosing an agent SDK is like choosing a vehicle: a durable, all-terrain truck (built for "can't fail" jobs) is the wrong choice for a quick errand, and a scooter is the wrong choice for hauling freight. The question isn't "which is objectively best" — it's "what does this specific job actually demand?"

### Real Example
Agent Factory frames the decision as a direct job-profile match:

| Job profile | Engine | Why |
|---|---|---|
| Can't fail | Dapr Agents wrapping an SDK | Durable execution, auto-recovery, full observability |
| Shouldn't fail, don't want to operate it yourself | Claude Managed Agents | Hosted and operated for you |
| Shouldn't fail, want portability | OpenAI Agents SDK | Production-grade, self-hosted, vendor-flexible |
| Nice if it works | OpenClaw-native | Lightweight, fast to deploy, good for routine tasks |

Notice the trade-offs are explicit, not hidden: OpenAI Agents SDK carries high vendor lock-in (the harness is tuned to OpenAI models) in exchange for self-hosted portability; Claude Managed Agents trades total lock-in for zero operational burden. Neither is a mistake — they're different bets matched to different job profiles.

### The Pitfall
Picking an SDK because it's the one you learned first, without checking whether your actual job profile matches its trade-offs. A "can't fail" production system built on a lightweight, fast-to-deploy runtime chosen for convenience is a mismatch that surfaces at the worst possible time — in production, under real failure conditions.

### Guiding Question (Answer This From Memory Later)
What questions should drive your choice of agent SDK or runtime?

---

## Your First Agent Concept: The Agent Triangle

### The Idea
Before writing any code, Agent Factory's framework for sketching a new agent concept is the **Agent Triangle**: every effective agent needs a clear **role**, specific **tools**, and well-defined **constraints**. Miss any one of the three, and the agent underperforms — no matter how good the underlying model is.

### In Simple Words
A new employee with no defined job title wanders. One with no tools (no phone, no system access) can think all day but never act. One with no boundaries takes actions nobody actually wanted. The Agent Triangle is the minimum job description every agent needs before it starts working — the same three questions you'd ask before hiring anyone.

### Real Example
Sketching a first agent concept for a customer-support Digital FTE, before any implementation:
- **Role**: "Handles Tier-1 customer support inquiries — order status, basic account questions, refund eligibility checks. Escalates anything else."
- **Tools**: `lookup_order(order_id)`, `check_refund_policy(plan, days_since_charge)`, `escalate_to_human(reason)`
- **Constraints**: "Never issues a refund directly — only checks eligibility and reports it. Never promises a delivery date the tool didn't return. Escalates immediately on any mention of legal threats."

Notice this sketch answers all three triangle questions before a single line of SDK code exists — which is exactly the "first 10%" discipline from Lesson 1's TDG cycle, applied to agent design instead of function design.

### The Pitfall
Defining tools and role clearly, but leaving constraints implicit ("it'll just know not to do that"). An unconstrained agent with the right tools and a clear role can still take actions nobody wanted — constraints aren't optional polish, they're the third leg of the triangle, and skipping them is exactly as broken as skipping tools.

### Guiding Question (Answer This From Memory Later)
What does the Agent Triangle say every effective agent concept needs?

---

## Summary

- **Agent Ops**: trace every run, sample and score production traffic, promote real failures into your eval suite — treat drift detection as proactive, not reactive.
- **Interoperability & Security**: MCP connects an agent to tools; A2A connects an agent to other agents — different layers, often used together, never a replacement for each other.
- **SDK Landscape**: match the runtime to the job profile (fail-tolerance, operational burden, lock-in comfort) — there's no universally "best" choice, only the right trade-off for the job.
- **Agent Triangle**: role, tools, and constraints — miss any one and the agent underperforms, regardless of model quality.
- **Why Together**: this closes out the conceptual foundation before Lesson 11's hands-on build — you now know how to operate an agent responsibly, connect it safely to other systems, choose the right foundation, and sketch its design before writing a single line of code.

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: In Agent Factory's observability stack, what does OpenTelemetry see that the agent SDK's own trace doesn't, and vice versa?
**Question 2**: If your agent needs to delegate work to a partner organization's agent it's never interacted with before, is MCP or A2A the right protocol, and why?
**Question 3**: Using the Agent Triangle, what's missing from an agent concept that has a clear role and specific tools, but no stated constraints?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector), Building Agent Factories — Chapter 61: Introduction to AI Agents (Agent Ops, Agent Interoperability & Security, The Agent SDK Landscape, Your First Agent Concept); Eval-Driven Development crash course (production observability and trace-to-eval pipeline); Payment-Enabled Agents crash course (A2A and discovery-layer comparison); Thesis (engine comparison and "picking your engine" table); Agent Factory Glossary (A2A, Agent Triangle entries).
