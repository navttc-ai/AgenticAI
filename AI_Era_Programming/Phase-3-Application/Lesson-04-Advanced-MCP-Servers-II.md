# Lesson 18: Advanced MCP Servers II — Transport, Scaling, Errors, Packaging

## Purpose

A server that works on your laptop isn't a server ready for production. This lesson closes out Chapter 67: deploying over StreamableHTTP for real, choosing stateful vs. stateless for scale, handling failures without crashing the whole run, and packaging a server so it's actually distributable.

## Guiding Questions

1. What makes StreamableHTTP the production transport, and what does `stateless_http=True` actually buy you?
2. What's the real operational trade-off between a stateful and a stateless server?
3. What's the difference between a transient failure and a permanent one, and why does that distinction matter for retries?
4. What does it mean to distribute an MCP server as "remote by design"?

---

## StreamableHTTP Transport in Production

### The Idea
StreamableHTTP is the transport that lets an MCP server run as a real network service — the same file that runs on your laptop for local testing runs identically in the cloud, reachable by any client that speaks HTTP.

### In Simple Words
`stdio` (from Lesson 15) only works when the client and server live on the same machine, like two people talking in the same room. StreamableHTTP is a phone line — the server can live anywhere, and any client with the number can call it, whether that's across the room or across the world.

### Real Example
```python
mcp = FastMCP("agent-factory-rag")

@mcp.tool()
def search_knowledge(query: str, limit: int = 5) -> list[dict]:
    """Search the knowledge base by meaning and return the closest chunks."""
    return search_quotes(query, limit)

if __name__ == "__main__":
    # Streamable HTTP, stateless — the production shape. The same file runs on
    # your laptop and in the cloud; host="0.0.0.0" binds all interfaces so a
    # container can route to it.
    mcp.run(transport="http", host="0.0.0.0", port=8000, stateless_http=True)
```
That single `mcp.run(...)` line is what turns a local script into a deployable service — no separate deployment-specific rewrite needed, because the transport was chosen production-ready from the start.

### The Pitfall
Building and testing entirely on `stdio` locally, then discovering at deploy time that the whole server needs restructuring for a network transport. Choosing StreamableHTTP from the beginning — even while developing locally — means the deployment step is a configuration change, not a rewrite.

### Guiding Question (Answer This From Memory Later)
What makes StreamableHTTP the production transport, and what does `stateless_http=True` actually buy you?

---

## Stateful vs. Stateless Servers: The Scaling Trade-Off

### The Idea
A stateless server treats every call as an independent request-response — exactly like an ordinary API call — so you can run many identical copies behind a load balancer, and any one of them can answer any request. A stateful server keeps a live session open (needed for streaming partial results or server-initiated notifications), but that pins each client to one specific server instance.

### In Simple Words
A stateless server is a bank teller window where any teller can help any customer — no history required, so you can open as many windows as you need under load. A stateful server is more like a dedicated account manager who remembers your ongoing conversation — genuinely useful for a long, evolving interaction, but you can't just swap in a different manager mid-conversation without losing context.

### Real Example
The default rule: **use stateless unless you have a specific reason not to.** Live streaming responses and server-initiated push notifications are the two documented reasons stateful earns its extra operational cost. Everything else — the vast majority of tool calls — fits the stateless shape cleanly, which is exactly why `stateless_http=True` was the default in the transport example above.

### The Pitfall
Choosing stateful "just in case" it's needed later, without a concrete requirement driving it. Every stateful server is one more piece of infrastructure that can't be freely load-balanced or scaled horizontally — pay that cost only when live streaming or server-initiated messages actually require it.

### Guiding Question (Answer This From Memory Later)
What's the real operational trade-off between a stateful and a stateless server?

---

## Error Handling & Recovery

### The Idea
Not every failure deserves a retry. A transient failure (a momentary network blip) might succeed on retry; a permanent failure (a malformed request, an unauthorized call) will fail identically every time no matter how many times you retry it. Recognizing which is which — and designing retries and recovery accordingly — is what keeps a server resilient instead of just noisy.

### In Simple Words
Retrying a flaky Wi-Fi connection makes sense — it might just be a bad moment. Retrying a request with the wrong password doesn't — it will fail exactly the same way every time, and retrying just wastes effort while looking like progress is happening.

### Real Example
The transient-vs-permanent distinction, applied to server-side error handling:
```python
async def call_payment_gateway(order_id: str, amount_cents: int) -> dict:
    try:
        return await gateway.charge(order_id, amount_cents)
    except GatewayTimeoutError:
        # Transient: a momentary network issue. Worth retrying.
        raise RetriableError("Gateway timed out, safe to retry")
    except CardDeclinedError as e:
        # Permanent: retrying changes nothing. Don't waste retry budget.
        raise NonRetriableError(f"Card declined: {e.reason}")
```
The broader principle: a card-declined error will be declined again on retry; a 401-unauthorized won't become a 200 just because you waited. Catching these specifically and handling them distinctly (log it, return cleanly, escalate) rather than blindly retrying everything is what separates graceful degradation from wasted retry budget.

### The Pitfall
Treating every caught exception the same way — either always retrying, or always failing immediately. Both are wrong for at least one of the two error categories. The discipline is classifying the failure first, then choosing the response that actually fits it.

### Guiding Question (Answer This From Memory Later)
What's the difference between a transient failure and a permanent one, and why does that distinction matter for retries?

---

## Packaging & Distribution

### The Idea
Distributing an MCP server well means shipping a *pointer* to it, not the server's logic and secrets themselves. A client connects to a remote server by URL (with credentials in a header), while the actual implementation, data, and secrets stay on infrastructure you control.

### In Simple Words
You don't hand someone a copy of your restaurant's kitchen to let them order food — you give them your phone number, and the kitchen stays exactly where it is, under your control, doing the actual cooking. Distributing an MCP server the same way means sharing the *address*, not the implementation.

### Real Example
```json
{
  "mcpServers": {
    "my-api": {
      "type": "http",
      "url": "https://api.yourdomain.com/mcp",
      "headers": { "Authorization": "Bearer ${MY_API_KEY}" }
    }
  }
}
```
Whoever consumes this configuration gets a *pointer* — the actual server, its data, and its business logic stay entirely on infrastructure you control. This is also what makes access revocable: a cancelled API key simply stops the connection from working, without anyone needing to track down and delete a distributed copy of server code running somewhere else.

### The Pitfall
Packaging a server in a way that ships its logic or secrets alongside the client (a local server whose code travels with whatever consumes it). That makes revoking access much harder — there's no single key to cancel, because the server's own code and data are now sitting on someone else's machine, outside your control.

### Guiding Question (Answer This From Memory Later)
What does it mean to distribute an MCP server as "remote by design"?

---

## Summary

- **StreamableHTTP**: the same server file runs locally and in production — choosing this transport from the start avoids a deployment-time rewrite.
- **Stateful vs. Stateless**: stateless (the default) scales freely behind a load balancer; stateful earns its cost only for live streaming or server-initiated messages.
- **Error Handling**: classify failures as transient (worth retrying) or permanent (retry changes nothing) before deciding how to respond — don't treat every error identically.
- **Packaging**: distribute a pointer (URL + credential), never the server's logic or secrets — this is also what makes access cleanly revocable.
- **Why Together**: these four close out Advanced MCP Server Development — a server that's reachable at scale, resilient to real failures, and distributed in a way that keeps you in control of it.

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: Why does choosing StreamableHTTP over `stdio` from the start avoid a rewrite later?
**Question 2**: Name the two documented reasons a stateful server is worth its extra operational cost.
**Question 3**: A server call fails with a card-declined error. Should it be retried? What about a gateway timeout?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector), Building a Digital FTE crash course — Concept 11 (transport comparison), Concept 15 (MCP under load, scaling and error-handling discipline); Postgres for AI crash course — Part 6 (StreamableHTTP production example); AI Agent Nervous System crash course — Concept 9 (transient vs. permanent failure patterns); Plugins crash course — Concept 6 (remote-by-design server distribution); Building Agent Factories — Chapter 67: Advanced MCP Server Development (StreamableHTTP Transport, Stateful vs Stateless Servers, Error Handling & Recovery, Packaging & Distribution).
