# Lesson 15: MCP Architecture, Transports, and the Tools/Resources Primitives

## Purpose

Every AI agent needs a standard way to reach external systems. This lesson introduces MCP — the protocol that solves that once instead of once per tool-agent combination — starting with what it actually is, how messages travel, and the first two of its three primitives.

## Guiding Questions

1. What is MCP, and what three things is it explicitly *not*?
2. What are the three transport options, and when does each one fit?
3. What makes a Tool the "model-controlled" primitive?
4. What makes a Resource the "app-controlled" primitive, and how is it different from a Tool?

---

## MCP Architecture: What It Is, and Isn't

### The Idea
MCP (Model Context Protocol) is the standard way an AI agent connects to external tools and data — write an integration once, and it works with any MCP-compatible client. It is a protocol, not a framework, not a hosted service, and not a security boundary.

### In Simple Words
Before MCP, connecting an agent to your database meant a custom integration for every agent-tool pairing — the same problem multiplied by every combination. MCP is a universal adapter: build the plug once, and any compatible device can use it, instead of wiring a new cable for every pairing.

### Real Example
Four things MCP explicitly is *not*, worth holding precisely because each one corrects a common misconception:
- **Not a framework** — your agent doesn't "use MCP" the way it uses the Agents SDK; your agent's MCP *client* speaks MCP to an MCP *server*. The Agents SDK includes an MCP client as its integration point.
- **Not a service** — there's no "MCP cloud." MCP servers are programs you run, or that vendors run for you (a filesystem server runs as a local subprocess; a hosted server runs wherever its owner deploys it).
- **Not a security boundary** — MCP defines transport and protocol; what tools a server exposes and what they're allowed to do is entirely the server's responsibility. A malicious MCP server can do anything its own server-side code does.
- **Not a replacement for `@function_tool`** — both still have a place; Lesson 13's decision tree between a local function tool and a custom MCP server still applies.

### The Pitfall
Assuming "the connection uses MCP" means the connection is automatically safe. Because MCP isn't a security boundary, the real trust boundary is still exactly what it was before MCP existed: the agent loop deciding which tools to call, and the sandbox those tools execute in.

### Guiding Question (Answer This From Memory Later)
What is MCP, and what three things is it explicitly *not*?

---

## Transport Layers: How MCP Messages Travel

### The Idea
MCP supports three transports for how a client and server actually exchange messages: `stdio` (a local subprocess), `streamable HTTP` (a remote server, the recommended choice for new production work), and legacy `SSE` (older remote deployments, being phased out in favor of streamable HTTP).

### In Simple Words
`stdio` is a conversation between two programs sitting on the same machine, passing notes directly — fast and simple, but only works locally. `streamable HTTP` is a phone call to a server somewhere else, over the regular internet, which is what you need once the server isn't running next to the agent anymore.

### Real Example
| Transport | When to use | Status |
|---|---|---|
| `stdio` | Local subprocess; agent and server on the same machine | Mature — default for local tools |
| `streamable HTTP` | Remote server; production deployments | **Recommended for new remote work** — single endpoint over plain HTTPS |
| `SSE` | Remote server; older deployments | Legacy — many servers still expose it, but new ones increasingly default to streamable HTTP |

Streamable HTTP itself comes in two flavors: **stateless** (each call is an independent request-response, exactly like an ordinary API call — the default to reach for, since any server instance behind a load balancer can answer) and **stateful** (a live session stays open for streaming partial results or server-initiated notifications — needed for long-running work, but pins a client to one specific server instance). Use stateless unless you have a specific reason not to.

### The Pitfall
Defaulting to a stateful session because it feels more "connected" or robust. Stateful sessions are genuinely more to operate — they can't be freely load-balanced — so the actual rule is to use stateless *unless* you have a concrete reason (live streaming, server-initiated messages) that requires the open session.

### Guiding Question (Answer This From Memory Later)
What are the three transport options, and when does each one fit?

---

## Tools: The Model-Controlled Primitive

### The Idea
A Tool is a function the model calls on its own, mid-reasoning, with no human pointing at it first — the only MCP surface that can be invoked automatically as part of the agent's decision-making.

### In Simple Words
Picture a workshop. A Tool is the cordless drill on the worker's belt — grabbed mid-job, no asking, no interruption to the flow of work. That's exactly why tools are the primitive an autonomous agent reaches for constantly: nothing else in MCP can be triggered without a human first pointing at it.

### Real Example
The comparison that makes this concrete:

| MCP surface | Who triggers it | Auto-called mid-reasoning? |
|---|---|---|
| **Tool** | The model, on its own | Yes |
| Resource | The user points at it | No |
| Prompt | The user picks it | No |

Only the Tool row says "yes" to autonomous triggering — which is why an agent that needs to decide, in the moment, whether to look something up or take an action, needs Tools specifically, not Resources or Prompts.

### The Pitfall
Building a capability as a Resource when the agent actually needs to reach for it autonomously mid-task. If the agent has to *wait* for a human to point at something before it can use it, that's the wrong primitive for anything the agent should be able to decide on its own — see the next concept for exactly when Resources are the right call instead.

### Guiding Question (Answer This From Memory Later)
What makes a Tool the "model-controlled" primitive?

---

## Resources: The App-Controlled Primitive

### The Idea
A Resource is read-only data the *user* points the agent at — passive by design, useless until someone chooses to hand it over. It cannot be auto-called mid-reasoning the way a Tool can.

### In Simple Words
Continuing the workshop picture: a Resource is a manual locked in a cabinet — genuinely useful, but useless until someone walks over and hands it across. The worker can't grab it mid-task the way they'd grab the drill; a human has to make the decision to retrieve it first.

### Real Example
A design choice from one of Agent Factory's own apps makes the trade-off concrete: that app intentionally exposes *only* Tools, not Resources or Prompts — because the app's whole shape requires it to decide on its own what to fetch or do next (search, pull a record, save a result), with no human in the loop to point at anything mid-task. That's a deliberate choice for *that* app's shape, not a rule that Resources are always wrong — an app where a user browses and selects reference material before asking a question is exactly the shape where Resources fit naturally.

### The Pitfall
Assuming Resources are simply "worse" or obsolete because Tools get more attention in most tutorials. Resources are the right primitive precisely when a human *should* be the one deciding what data enters the conversation — treating everything as a Tool removes that deliberate human checkpoint even in situations where you'd actually want it.

### Guiding Question (Answer This From Memory Later)
What makes a Resource the "app-controlled" primitive, and how is it different from a Tool?

---

## Summary

- **MCP Architecture**: a protocol (not a framework, service, or security boundary) that lets one integration work across any compatible client — the trust boundary remains the agent loop and sandbox, exactly as before MCP.
- **Transports**: `stdio` for local subprocesses, `streamable HTTP` (stateless by default) for production remote servers, legacy `SSE` being phased out.
- **Tools**: the model-controlled primitive — callable autonomously mid-reasoning, the cordless drill on the belt.
- **Resources**: the app/user-controlled primitive — passive, read-only, requires a human to point at it first, the manual in the cabinet.
- **Why Together**: these four concepts are the foundation everything else in MCP builds on — you can't design a good MCP server without knowing what transport fits your deployment and which primitive matches whether the *model* or the *user* should be making the call.

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: Name the three things Agent Factory explicitly says MCP is *not*.
**Question 2**: Why is stateless streamable HTTP the default recommendation over stateful, and what's the one situation where stateful is worth it?
**Question 3**: If an agent needs to autonomously decide, mid-conversation, whether to look up a customer's order status, should that capability be exposed as a Tool or a Resource, and why?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector), Building a Digital FTE crash course — Concept 11 (What MCP Is and Isn't, transport comparison table); Connector-Native Apps crash course — Concept 3 (Tools vs. Resources vs. Prompts, the workshop analogy); Postgres for AI crash course — Part 6 (transport role: dev-time stdio vs. production streamable HTTP); Building Agent Factories — Chapter 66: MCP Fundamentals (MCP Architecture Overview, Transport Layers, Tools, Resources).
