# Lesson 16: Using MCP — Prompts, Client Configuration, and Debugging

## Purpose

You know what MCP is and its first two primitives. This lesson finishes the fundamentals: the third primitive (Prompts), how to actually wire a server into your client, when to reach for an existing community server instead of building your own, and how to diagnose it when something goes wrong.

## Guiding Questions

1. What makes a Prompt the "user-controlled" primitive, distinct from both Tools and Resources?
2. How do you actually register an MCP server with a client like Claude Code or OpenCode?
3. What's the rule for deciding whether to connect an MCP server at all?
4. What are the most common MCP failure patterns, and what do their symptoms tell you?

---

## Prompts: The User-Controlled Primitive

### The Idea
A Prompt is a pre-crafted instruction template the *user* selects — not called automatically by the model like a Tool, and not just passively read like a Resource, but explicitly picked from a menu. It's for standardizing how a task gets done across many uses or many people.

### In Simple Words
Continuing the workshop picture from Lesson 15: if a Tool is the drill grabbed mid-job and a Resource is the manual in the cabinet, a Prompt is a form the worker has to stop and pick off a shelf before starting a specific, standardized kind of job. It's not automatic, and it's not just reference material — it's a chosen starting point.

### Real Example
Agent Factory's own worked comparison across all three primitives, applied to real scenarios:
- *"The agent reads the current text of a policy document on demand, but never writes it"* → **Resource** (passive, read-only, the model looks at it but doesn't autonomously decide to fetch it without being pointed there)
- *"The agent issues a refund through the payment gateway"* → **Tool** (an action the model decides to take mid-reasoning)
- *"Every Worker on the team should summarize incidents the same way, from one shared, versioned template"* → **Prompt** (a standardized starting point a user selects, ensuring consistency across many different agents or people using it)

That third case is exactly what Prompts are for: not something the model reaches for autonomously, and not passive reference data, but a chosen, versioned template that keeps output consistent across an entire team.

### The Pitfall
Reaching for a Tool when what you actually want is standardization and consistency across many uses. If the goal is "everyone on the team should start from the same template," building that as a Tool the model might-or-might-not call loses the consistency guarantee — a Prompt, explicitly selected, is what actually enforces it.

### Guiding Question (Answer This From Memory Later)
What makes a Prompt the "user-controlled" primitive, distinct from both Tools and Resources?

---

## Configuring MCP Clients

### The Idea
Registering an MCP server with a client is a per-tool configuration step — the server itself is a universal standard, but where and how you register it differs by client. Claude Code and OpenCode, for instance, keep their connections in entirely different files, even though the same server works with both.

### In Simple Words
The server is like a universal power outlet — it works the same way no matter which building it's installed in. But *registering* that outlet with each building's electrical panel is a separate step you do once per building; the outlet doesn't automatically know about every building that might want to use it.

### Real Example
```bash
# Claude Code: add via CLI, writes to ~/.claude.json (personal) by default
claude mcp add --transport http sentry https://mcp.sentry.dev/mcp
# --scope project writes a shareable .mcp.json in the project root instead

# Then check status and log in to anything needing OAuth:
# run /mcp inside Claude Code
```
```json
// OpenCode: declared directly in opencode.json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "sentry": { "type": "remote", "url": "https://mcp.sentry.dev/mcp", "oauth": {} },
    "context7": { "type": "remote", "url": "https://mcp.context7.com/mcp" }
  }
}
```
Because MCP is an open standard, the *server* (`mcp.sentry.dev`, in this example) works identically with either client — but the configuration doesn't transfer between them, so each client needs its own registration step.

### The Pitfall
Confusing the MCP configuration file with a client's general settings file. Agent Factory flags this explicitly for Claude Code: the MCP config is *not* `.claude/settings.json` (that's for permissions and hooks) — a common early mistake is editing the wrong file and wondering why the server never connects.

### Guiding Question (Answer This From Memory Later)
How do you actually register an MCP server with a client like Claude Code or OpenCode?

---

## Using Community MCP Servers: The Connect/Skip Decision

### The Idea
Not every capability deserves an MCP connection. Use MCP when the service genuinely needs the agent to log in or stay connected (Google Calendar, a password-protected database); skip it when the agent can get the same result with a simple terminal command — and even when a connection is warranted, connect only what you actually need, since each one can consume context space whether you're using it or not.

### In Simple Values
Installing every available MCP server "just in case" is like keeping every possible tool out on your workbench at once — it doesn't make you faster, it just makes it harder to find the one you actually need, and some tools (like the GitHub MCP server) take up a lot of workbench space even sitting idle.

### Real Example
The concrete rule, with a real comparison: instead of connecting to GitHub through MCP just to check issues, the agent can simply run `gh issue list` in the terminal — no login, no standing connection, no extra setup. Reach for MCP specifically when the task needs authenticated, persistent access a one-off terminal command can't provide.

The cost of over-connecting is real and specific: some MCP servers "tend to add a lot of tokens and can easily exceed the context limit" (OpenCode's own documentation, on the GitHub MCP server specifically). Claude Code mitigates this somewhat by deferring tool definitions until they're about to be used; OpenCode loads them upfront, so being selective matters even more there.

### The Pitfall
Installing ten MCP servers because they're available, rather than the one or two the current task actually needs. Every connected server is context overhead whether or not you use it in a given conversation — start minimal, and add connections only when a specific, recurring need justifies the standing cost.

### Guiding Question (Answer This From Memory Later)
What's the rule for deciding whether to connect an MCP server at all?

---

## Debugging and Troubleshooting MCP

### The Idea
Most MCP failures fall into a small number of recognizable patterns — a stale tool-list cache, a startup timeout too short for a heavy server, a misconfigured connection pool — and recognizing the pattern from its symptom is faster than guessing.

### In Simple Words
Like the six-error-category table from Lesson 4's Python debugging, MCP has its own small set of recurring failure shapes. You don't need to have memorized every possible bug — you need to recognize which *category* a symptom belongs to, because that tells you exactly where to look.

### Real Example
A few of Agent Factory's own documented failure patterns, symptom to cause:

| Symptom | Likely cause |
|---|---|
| MCP tool not appearing in the agent | Server not registered, or the tool-list cache is stale — check your server registration and try disabling caching temporarily |
| MCP server hangs on startup (especially servers loading ML models) | Default connection timeout is too short for a server doing heavy work at import time — lengthen the timeout window |
| Agent timing out on operations under load | The MCP server's own connection pool (e.g., to a database) is too small for the concurrent load |
| A "general SQL tool" or similarly broad capability shows up unexpectedly | The agent is still wired to a dev-only admin server in a production path — this is a scope leak, not a bug in the protocol itself |

### The Pitfall
Assuming a connection failure means the protocol itself is broken. In practice, the overwhelming majority of MCP issues trace back to one of a handful of configuration or scoping mistakes — a stale cache, a too-short timeout, a dev tool leaking into production — not a flaw in MCP as a standard. Check the common patterns before assuming something exotic is wrong.

### Guiding Question (Answer This From Memory Later)
What are the most common MCP failure patterns, and what do their symptoms tell you?

---

## Summary

- **Prompts**: user-selected, versioned templates for standardizing a task across a team — not autonomous like Tools, not passive like Resources.
- **Client Configuration**: the server is a universal standard, but registration is per-client (Claude Code's `claude mcp add` / `.mcp.json` vs. OpenCode's `opencode.json`) — and easy to confuse with unrelated settings files.
- **Community Servers**: connect only what you need; skip MCP entirely when a simple terminal command gets the same result, since unused connections still cost context.
- **Debugging**: most failures are one of a handful of recognizable patterns (stale cache, short timeout, scope leak, undersized pool) — recognize the category from the symptom.
- **Why Together**: this closes MCP Fundamentals — you now know all three primitives, how to wire a client to a server, when a connection is even worth making, and how to diagnose it when it isn't working.

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: Give an example of a capability that fits a Prompt rather than a Tool, and explain why.
**Question 2**: Why might the same MCP server work perfectly in Claude Code but need a completely separate setup step in OpenCode?
**Question 3**: A developer notices a tool they just added to their MCP server isn't showing up in their agent's available tools. What's the most likely cause, based on this lesson's debugging table?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector), Building a Digital FTE crash course — Concept 11 (Tool/Resource/Prompt worked examples), Concept 15 and Quick Reference (MCP under load, debugging table); Agentic Coding crash course — Concept 12 (MCP client configuration, connect/skip rules); Building Agent Factories — Chapter 66: MCP Fundamentals (Prompts, Configuring MCP Clients, Using Community MCP Servers, Debugging and Troubleshooting MCP).
