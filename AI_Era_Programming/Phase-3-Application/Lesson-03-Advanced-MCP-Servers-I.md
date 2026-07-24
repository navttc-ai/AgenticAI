# Lesson 17: Advanced MCP Servers I — Context, Sampling, Notifications, Roots

## Purpose

Chapter 66 taught you MCP fundamentals with basic decorators. This lesson starts the production patterns: giving a tool access to session state, letting a server ask an LLM for help mid-task, streaming progress back during long operations, and scoping exactly what part of the filesystem a server can touch.

## Guiding Questions

1. What does injecting a Context object into a tool actually give you access to?
2. What does it mean for a server to use "sampling," and why does that shift cost?
3. Why would a long-running tool send progress notifications instead of just returning when it's done?
4. What do Roots let a server-side operation do that raw filesystem access doesn't?

---

## Context Object & Server Lifespan

### The Idea
A Context object, injected into a tool function, gives that tool access to things beyond its own arguments — session state, logging, progress reporting, and other capabilities tied to the current request rather than passed explicitly as parameters.

### In Simple Words
Without a context object, a tool only knows what you hand it directly in its arguments — like a new employee who only knows what's written on their specific task ticket. A context object is that employee also having access to the building directory, the shared calendar, and a way to radio the front desk — session-wide resources that exist alongside whatever specific task they were just handed.

### Real Example
A pattern from Agent Factory's own connector work shows the underlying idea clearly, even outside FastMCP's specific Context API: a `begin_session` tool that runs first, mints a session token tied to the verified caller's identity, and returns it — every subsequent tool call in that session checks the token before doing anything:
```python
@mcp.tool()
def begin_session() -> dict:
    """Call this FIRST on any new request. Returns session state and a token
    the other tools require."""
    sub = verified_claims(current_token())["sub"]
    return {
        "session": new_session_token(sub),   # gates every other tool
        "rules": config_get_rules(),
        "state": user_get_state(sub),
    }
```
A Context object formalizes this same idea as SDK-native injection: instead of manually threading a session token through every tool's arguments, the tool function receives a `ctx` parameter the framework populates automatically, carrying exactly this kind of session-scoped state.

### The Pitfall
Passing session state as an ordinary tool argument that the *model* has to remember and re-supply on every call. That's fragile — the model can forget, mistype, or (per Lesson 11's context-object lesson) not be trusted with sensitive state at all. A Context object keeps that state server-side, tied to the session, not dependent on the model faithfully echoing it back each time.

### Guiding Question (Answer This From Memory Later)
What does injecting a Context object into a tool actually give you access to?

---

## Sampling: Servers Calling LLMs

### The Idea
Sampling lets an MCP server ask the *client's* connected LLM to generate something, mid-tool-execution, via a callback — rather than the server needing its own separate API key and model access. The cost of that LLM call is shifted onto whichever client is running the request, not the server.

### In Simple Words
Normally, a tool just does its job and returns data — like a librarian handing you a book. Sampling is the librarian, partway through helping you, asking the reader sitting at your table to quickly summarize a paragraph for them, using the reader's own knowledge rather than calling their own separate expert. The server borrows the client's model instead of hiring its own.

### Real Example
The conceptual shape (FastMCP-style):
```python
@mcp.tool()
async def summarize_ticket(ctx: Context, ticket_text: str) -> str:
    """Summarize a long support ticket into one sentence, using the
    caller's own connected model rather than a separate API call."""
    result = await ctx.session.create_message(
        messages=[{"role": "user", "content": f"Summarize in one sentence: {ticket_text}"}],
        max_tokens=100,
    )
    return result.content
```
The server never needed its own OpenAI or Anthropic API key for this — it asked the client (whichever agent or app is running the session) to make that call on its behalf, through the MCP protocol itself.

### The Pitfall
Assuming sampling makes an LLM call "free" for the server. It doesn't eliminate the cost — it *relocates* it to the client's own model budget. A server that samples heavily can quietly inflate a client's bill in ways that aren't visible from the server's side at all, since the server never sees a bill for it.

### Guiding Question (Answer This From Memory Later)
What does it mean for a server to use "sampling," and why does that shift cost?

---

## Progress & Logging Notifications

### The Idea
For a tool call that takes a while, a server can send progress notifications — intermediate updates — back to the client while the work is still running, instead of the client sitting with no feedback until a single final result arrives.

### In Simple Words
Compare a pizza tracker to a delivery that just shows up with no updates at all. Both eventually deliver the pizza, but one lets you know it's in the oven, then out for delivery — you're not left wondering if anything is happening. Progress notifications are that tracker, applied to a long-running tool call.

### Real Example
The conceptual shape:
```python
@mcp.tool()
async def reindex_documents(ctx: Context, collection: str) -> str:
    """Rebuild the search index for a collection. Can take several minutes."""
    documents = await fetch_documents(collection)
    for i, doc in enumerate(documents):
        await index_one(doc)
        await ctx.report_progress(progress=i + 1, total=len(documents))
    return f"Reindexed {len(documents)} documents in '{collection}'."
```
A client watching this call can show "342 of 1,000 documents reindexed" instead of an indefinite spinner — genuinely useful for anything long enough that a user might otherwise wonder if it's stuck.

### The Pitfall
Adding progress reporting to a tool call that finishes in under a second. The reporting itself has a small overhead, and for genuinely fast operations it adds noise without adding useful information — reserve it for operations where a human (or a monitoring system) benefits from knowing it's still working, not finished, and roughly how far along.

### Guiding Question (Answer This From Memory Later)
Why would a long-running tool send progress notifications instead of just returning when it's done?

---

## Roots: File System Permissions

### The Idea
Roots let a server (or the client granting it access) scope exactly which parts of the filesystem an operation is allowed to touch — the same underlying principle as file-access permission scoping in any agent tool, applied specifically to MCP servers that need filesystem access.

### In Simple Words
Giving an agent your entire hard drive to "help organize files" is like handing a contractor keys to your whole house to fix one leaky faucet. Roots are the equivalent of saying "you may work in this bathroom only" — scoped, specific, and revocable, rather than blanket access that's much harder to reason about or trust.

### Real Example
Agent Factory's own permission-ladder discipline (documented for general agent tools, and the same principle Roots enforce specifically for MCP) scales access gradually and explicitly:

| Comfort level | What's allowed |
|---|---|
| First sessions | Read-only access to a single small folder |
| After 2–3 successful runs | Read *and* write inside that one specific folder |
| After a clean week | Read across a project tree, write inside a scoped subfolder |
| Trusted | Tool-specific permissions ("rename PDFs in this folder"), never open-ended |

The conceptual shape for enforcing this in an MCP server:
```python
def is_path_allowed(requested_path: str, roots: list[str]) -> bool:
    """Check that a requested path falls inside one of the granted roots."""
    return any(requested_path.startswith(root) for root in roots)
```
The scope grows with track record, never with blanket trust in the tool itself — the same discipline whether it's a human approving folder access in a chat app, or a Roots declaration scoping what an MCP server's file tools can touch.

### The Pitfall
Granting broad root access "to save time" instead of scoping to exactly what the immediate task needs. The permission ladder's whole point is that scope should grow with demonstrated reliability — starting broad and narrowing later, if you even remember to, is the wrong direction for a security boundary.

### Guiding Question (Answer This From Memory Later)
What do Roots let a server-side operation do that raw filesystem access doesn't?

---

## Summary

- **Context Object**: injected into a tool, carries session-scoped state (like a verified identity or session token) that the model doesn't have to remember and re-supply on every call.
- **Sampling**: a server asks the client's own connected LLM to generate something mid-tool-call — cost shifts to the client's budget, not the server's.
- **Progress Notifications**: intermediate updates during a long-running tool call, useful when a human or monitor benefits from knowing it's still working — skip it for fast operations.
- **Roots**: scope exactly which filesystem paths an operation can touch, following the same "start narrow, widen only with track record" discipline as any agent permission.
- **Why Together**: these four patterns are what separate a toy MCP server from one built for real, longer-running, more sensitive production work — session awareness, borrowed intelligence, visible progress, and scoped trust.

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: Why is a Context object generally safer than passing session state as a regular tool argument the model has to remember?
**Question 2**: If a server uses sampling heavily, whose budget absorbs the LLM cost — the server's or the client's?
**Question 3**: Using the permission-ladder table, what should a brand-new, untrusted MCP server's file access look like on day one?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector), Connector-Native Apps crash course — Concept 10 (session-init contract, session token pattern); Cowork crash course and AI Prompting in 2026 (folder permission scoping, the permission ladder); Building Agent Factories — Chapter 67: Advanced MCP Server Development (Context Object & Server Lifespan, Sampling, Progress & Logging Notifications, Roots).
