# Lesson 13: Grounding Your Agent — MCP, RAG, and the Digital FTE Capstone

## Purpose

An agent with only its training data is guessing. This lesson connects your agent to real external tools via MCP and real external knowledge via RAG, then closes out Chapter 62 with the capstone that assembles everything into one production-shaped Digital FTE.

## Guiding Questions

1. How does an OpenAI Agents SDK agent actually connect to and use an MCP server?
2. What's the difference between an agent's memory and its knowledge?
3. What capabilities does the Customer Support Digital FTE capstone require you to combine?

---

## MCP Integration: Servers as Just Another Tool Source

### The Idea
The OpenAI Agents SDK ships a built-in MCP client. You open a connection to an MCP server, hand it to your agent, and the SDK does the rest: it asks the server what tools it has, lays those tools in front of the model right alongside the `@function_tool`s you wrote yourself. The model can't tell an MCP tool from a local function tool — and it doesn't need to.

### In Simple Words
From the model's point of view, a tool is a tool. Whether that tool is a plain Python function you wrote in the same file, or a whole separate server running somewhere else, the model just sees a name, a description, and a schema — the same way you don't think differently about calling a coworker who sits next to you versus one on a different floor, as long as you know how to reach them.

### Real Example
Four things the SDK handles for you once you ask for them:
- **Open and close connections cleanly** — an MCP connection holds something open (a subprocess for stdio, an HTTPS session for remote); the SDK's connection objects are built to be opened and closed as a managed block.
- **Cache the tool list in production** — by default the agent re-asks the server "what tools do you have?" on every single run, a wasted round-trip; turning on caching makes it ask once. (Leave caching off while building so tool changes show up immediately.)
- **Servers stack** — you can hand your agent several MCP servers at once, and the model simply sees the combined set of tools from all of them.
- **Gate dangerous tools behind approval** — by default tool calls run with no confirmation; for sensitive ones (anything that drops or rewrites data), you can require a human to approve each call before it runs.

### The Pitfall
Leaving tool-list caching on while you're still actively changing the MCP server's tools during development. The agent keeps using its stale cached list, and a tool you just added or renamed silently doesn't show up — not because the connection is broken, but because the cache hasn't been told to refresh.

### Guiding Question (Answer This From Memory Later)
How does an OpenAI Agents SDK agent actually connect to and use an MCP server?

---

## RAG: The Agent's Knowledge, Distinct From Its Memory

### The Idea
Every agent has two separate kinds of state: **memory** (what it recalls from the running conversation — handled by Sessions from Lesson 12) and **knowledge** (durable, searchable context it looks up on demand, across far more data than fits in any context window — handled by RAG). Wiring RAG into an agent means giving it a retrieval tool, the same way you'd give it any other tool.

### In Simple Words
Memory is what you remember from the conversation you're having right now. Knowledge is the reference library you can walk over and consult without having memorized its contents. An agent wired to both can remember what you just told it *and* look up facts it never had memorized — the same distinction between a person's working memory and their ability to look something up.

### Real Example
Two equivalent ways to give an agent retrieval as a tool:
```python
# Option A: as an MCP server — the retrieval tools just appear in the agent's toolbox
agent = Agent(
    name="SupportAgent",
    instructions="Answer questions using search_knowledge when you need grounded facts.",
    mcp_servers=[knowledge_server],   # search_knowledge, answer_question appear automatically
)

# Option B: as a plain function tool — same retrieval, wrapped in-process
@function_tool
def search_knowledge(query: str, limit: int = 5) -> list[dict]:
    """Search the knowledge base by meaning and return the closest chunks.
    Use this when you need grounded facts from the user's own data."""
    return vector_search(query, limit)

agent = Agent(
    name="SupportAgent",
    instructions="Answer questions using search_knowledge when you need grounded facts.",
    tools=[search_knowledge],
)
```
Both are the same underlying retrieval — the choice between MCP and a plain function tool is an implementation detail, not a difference in what the agent can do. What matters is that the tool fetches only the relevant chunks and leaves everything irrelevant out — that discipline is what keeps retrieved context from flooding the model's window.

### The Pitfall
Treating RAG as a replacement for Sessions, or vice versa. An agent with only knowledge and no memory re-explains itself every turn; an agent with only memory and no knowledge can never answer anything outside what's already been said in the conversation. Production agents typically need both, serving genuinely different jobs.

### Guiding Question (Answer This From Memory Later)
What's the difference between an agent's memory and its knowledge?

---

## Capstone: The Customer Support Digital FTE

### The Idea
The Chapter 62 capstone combines every concept from Lessons 11–13 into one working system: a Customer Support Digital FTE that routes inquiries to specialist agents, maintains conversation context, validates inputs with guardrails, persists conversations, provides full observability, and integrates external knowledge — the complete shape of a production agent, not a demo.

### In Simple Words
Every lesson so far taught you one instrument. The capstone is the first time you play the whole orchestra together: the tools from Lesson 11, the handoffs/guardrails/sessions/tracing from Lesson 12, and the MCP/RAG grounding from this lesson, all wired into one system that has to actually work end to end, not just in isolated examples.

### Real Example
The capstone's Customer Support Digital FTE is required to demonstrate, together:
- **Routes inquiries to specialist agents** (FAQ, Booking, Escalation) — Lesson 12's handoffs, applied for real
- **Maintains conversation context across handoffs** — the specialist picks up exactly where triage left off
- **Validates inputs with guardrails** — abuse detection, PII filtering, using the parallel/blocking distinction from Lesson 12
- **Persists conversations with sessions** — so a returning customer's context survives across turns
- **Provides full observability through tracing** — every routing decision, every tool call, inspectable
- **Integrates external knowledge via MCP and RAG** — this lesson's grounding, wired into the same system

The capstone also includes monetization models (subscription, success fee, hybrid) — a reminder that this isn't just a technical exercise; it's the shape of something meant to be shipped and priced as a real Digital FTE.

### The Pitfall
Building each piece in isolation and assuming they'll compose cleanly at the end. A guardrail that works fine standalone can interact badly with a handoff (does the specialist inherit the triage agent's guardrails, or need its own?); a session that works for one agent needs to carry correctly across a handoff to another. The capstone's whole point is testing that composition, not just each piece alone.

### Guiding Question (Answer This From Memory Later)
What capabilities does the Customer Support Digital FTE capstone require you to combine?

---

## Summary

- **MCP Integration**: the SDK treats MCP tools identically to local function tools — cache the tool list in production, leave it off while iterating, and gate destructive tools behind human approval.
- **RAG**: knowledge (searchable, durable context) is distinct from memory (conversation recall) — most production agents need both, wired as separate but complementary tools.
- **The Capstone**: routing, context, guardrails, persistence, observability, and knowledge grounding all have to work *together*, not just individually — that composition is what the capstone actually tests.
- **Why Together**: this closes Chapter 62 — you now have every piece of a production-shaped agent, and the capstone is where you prove they compose into something that actually ships.

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: Why might a developer see a tool they just added to an MCP server not show up in their agent's available tools?
**Question 2**: If an agent needs to both recall what a customer said five minutes ago and look up a policy document it's never seen before, what two capabilities does it need, and are they the same thing?
**Question 3**: Name three of the six capabilities the Customer Support Digital FTE capstone requires you to combine.

---

**Sources**: Agent Factory System of Record (via SoR MCP connector), Building a Digital FTE crash course — Concept 13 (Connecting MCP to the OpenAI Agents SDK); Postgres for AI crash course — Part 8 (Hand It to an Agent — memory vs. knowledge, RAG as a tool); Building Agent Factories — Chapter 62: OpenAI Agents SDK (MCP Integration, RAG with FileSearchTool, Capstone: Building a Customer Support Digital FTE).
