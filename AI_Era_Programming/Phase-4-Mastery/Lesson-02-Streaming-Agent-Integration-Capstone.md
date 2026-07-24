# Lesson 25: Streaming, Agent Integration, and the Capstone

## Purpose

This is the last lesson of the entire course. It closes FastAPI — and the whole curriculum — with the pieces that turn your API from a request-response service into something a live UI and a real agent can both use: startup/shutdown lifecycle, real-time streaming, and the key insight that makes an API into a set of agent tools.

## Guiding Questions

1. What problem do lifespan events solve that can't be handled inside individual route handlers?
2. What does SSE streaming give a user that a normal request-response call can't?
3. What's the key insight behind "APIs are functions, functions become tools, agents use tools"?

---

## Lifespan Events: Startup and Shutdown

### The Idea
Lifespan events let you run setup code once when the app starts (opening a database connection pool, checking which backends are available) and cleanup code once when it shuts down (closing connections cleanly) — rather than repeating that setup inside every single route.

### In Simple Words
A restaurant doesn't re-light the stove and re-check the fridge temperature for every single customer order — it does that once when the kitchen opens for the day, and shuts everything down once when it closes. Lifespan events are that opening and closing ritual for your API, run exactly once regardless of how many requests come in between.

### Real Example
```python
from contextlib import asynccontextmanager
from fastapi import FastAPI

@asynccontextmanager
async def lifespan(app: FastAPI):
    # Startup: runs once, before the app starts accepting requests
    app.state.db_pool = await create_db_pool()
    print("Database pool ready")
    yield
    # Shutdown: runs once, after the app stops accepting new requests
    await app.state.db_pool.close()
    print("Database pool closed cleanly")

app = FastAPI(lifespan=lifespan)

@app.get("/health")
def health_check() -> dict[str, bool]:
    return {"database_ready": app.state.db_pool is not None}
```
This is the same graceful-degradation instinct from Lesson 23's `GET /health` pattern, formalized: connections and expensive resources get set up once, at a known moment, and the health check can honestly report on what's actually ready rather than guessing per-request.

### The Pitfall
Opening a fresh database connection inside every route handler instead of once at startup. Beyond the wasted overhead of connecting on every single request, it also means connection cleanup is scattered everywhere instead of guaranteed in one place when the app shuts down.

### Guiding Question (Answer This From Memory Later)
What problem do lifespan events solve that can't be handled inside individual route handlers?

---

## Streaming with SSE

### The Idea
Server-Sent Events (SSE) let a server push a sequence of updates to a client over a single, ongoing HTTP connection — instead of the client waiting silently for one complete response, it receives a stream of smaller updates as they become available.

### In Simple Words
A normal API call is ordering food and waiting silently at the counter until the entire meal arrives at once. Streaming is a kitchen-pickup app that pings you along the way — "order received," "in the fryer," "almost ready" — a sequence of small notifications arriving over time instead of one big delivery at the end. For a chat-style API, that's the difference between a blank screen for ten seconds and text appearing word by word as it's generated.

### Real Example
```python
from fastapi import FastAPI
from fastapi.responses import StreamingResponse
import asyncio

app = FastAPI()

async def event_generator():
    for i in range(5):
        yield f"data: Step {i + 1} of 5 complete\n\n"
        await asyncio.sleep(1)
    yield "data: Done\n\n"

@app.get("/progress")
async def stream_progress():
    return StreamingResponse(event_generator(), media_type="text/event-stream")
```
The `yield` keyword is doing the same job here as it did back in Lesson 6's generators — handing back one piece at a time rather than the whole result at once. A live cricket-score ticker that updates without a page refresh is the same underlying mechanism: the server keeps the connection open and pushes each new update as it happens, one-way, over standard HTTP.

### The Pitfall
Adding streaming before the plain, synchronous version works reliably. Streaming buys a live-feeling UI at the cost of debugging difficulty — when a synchronous call fails, you get one clean error; when a stream fails halfway through, you get a half-printed response and no obvious culprit. Get the plain version solid first, then layer streaming on top of something already known to work.

### Guiding Question (Answer This From Memory Later)
What does SSE streaming give a user that a normal request-response call can't?

---

## Agent Integration: APIs Are Functions, Functions Become Tools, Agents Use Tools

### The Idea
The key insight connecting this entire FastAPI chapter back to the Custom Agent Development track: a well-designed API endpoint is just a function; wrap that function with `@function_tool` (Lesson 11), and it becomes something an agent can call directly — the exact same Task API you built in this chapter can become one of an agent's tools with almost no extra work.

### In Simple Words
Your FastAPI route already does one clean job with a typed input and output — that's exactly the shape `@function_tool` needs. The work of building a good API (clear inputs, clear outputs, sensible errors) turns out to be *the same work* as building a good agent tool. You weren't building two separate things this whole course — you were building one thing that serves two audiences: a human calling it over HTTP, and an agent calling it as a tool.

### Real Example
```python
# Your FastAPI route, already built in Lesson 23
@app.post("/notes")
def create_note(note: NoteIn) -> NoteOut:
    ...

# The same capability, exposed to an agent as a tool — almost no new code
from agents import function_tool

@function_tool
async def create_note_tool(title: str, body: str) -> str:
    """Create a new note with a title and body. Returns the created note's id."""
    result = await call_internal_api("/notes", {"title": title, "body": body})
    return f"Created note {result['id']}: {result['title']}"

support_agent = Agent(
    name="NotesAssistant",
    instructions="Help the user manage their notes.",
    tools=[create_note_tool],
)
```
Well-designed agent tools follow the same discipline good API design already teaches: name the action plainly (not `process`, but `create_note`), keep the input schema narrow and typed, and return errors the caller can act on rather than a sentence to interpret. None of that is new — it's Lesson 11's function-tool discipline, applied to an API you'd build well anyway.

### The Pitfall
Treating "build a good API" and "build good agent tools" as two separate design problems requiring two separate efforts. The disciplines are close to identical — clear naming, narrow typed contracts, actionable errors — which means an API built well from the start is most of the way to being agent-ready with very little extra work.

### Guiding Question (Answer This From Memory Later)
What's the key insight behind "APIs are functions, functions become tools, agents use tools"?

---

## Summary

- **Lifespan Events**: run setup once at startup and cleanup once at shutdown — the same graceful-degradation discipline from earlier lessons, formalized into a known lifecycle.
- **Streaming with SSE**: pushes updates to a client over time instead of one silent wait — get the plain synchronous version solid before adding this on top.
- **Agent Integration**: a well-designed API is already most of the way to being a set of agent tools — the same discipline (clear names, narrow schemas, actionable errors) serves both a human caller and an agent caller.
- **Why Together**: this closes the entire course. Every discipline from Lesson 1's TDG cycle through this lesson's agent integration converges here — you've built a system that's tested, typed, secured, observable, and finally, reachable by both people and agents.

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: Why is opening a database connection inside every route handler worse than doing it once at startup?
**Question 2**: What specific trade-off should make you build the plain synchronous version of an endpoint before adding SSE streaming to it?
**Question 3**: What does the phrase "APIs are functions, functions become tools, agents use tools" actually mean about the relationship between good API design and good agent tool design?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector), Build AI Agents crash course — Concept 7 (Streaming Responses, the kitchen-pickup-app analogy); Payment-Enabled Agents crash course — Concept 3 (wrapping capabilities as `@function_tool`s); Designing Agent Experiences crash course — Concept 13 (Agent Experience design checklist: naming, schemas, actionable errors, idempotency); Agent Factory Glossary (SSE entry); Building Agent Factories — Chapter 70: FastAPI for Agents (Lifespan Events, Streaming with SSE, Agent Integration, Capstone: Agent-Powered Task Service).

---

## This completes the entire AI-Era Programming curriculum.

**25 lessons across four tracks — Python in the AI Era (1–8), Custom Agent Development (9–14), MCP Development (15–21), and FastAPI for Agents (22–25) — all delivered, grounded in Agent Factory's System of Record, with real code examples throughout.**

The thread running through all 25: specify before you generate, test before you trust, verify before you ship — the same TDG discipline from Lesson 1, applied all the way from reading a traceback to building an agent-powered, streaming, authenticated production API.
