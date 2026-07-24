# Lesson 11: Building Your First OpenAI Agent

## Purpose

Architecture becomes real code in this lesson. You'll meet the three primitives every agent codebase is built from, learn how function tools become the model's callable contract, pass trusted state the model can't fake, and compose multiple agents together.

## Guiding Questions

1. What are the three primitives that appear in every OpenAI Agents SDK codebase?
2. What makes a function tool safe to hand to a model?
3. Why does an agent need a context object separate from what the model can see and control?
4. What's the difference between `Agent.as_tool()` and `handoff()` for combining agents?

---

## The SDK's Three Primitives: Agent, Runner, function_tool

### The Idea
Three names appear in every agent codebase built on the OpenAI Agents SDK: `Agent` (an LLM equipped with instructions and tools), `Runner` (the loop that drives it), and `@function_tool` (the decorator that turns a Python function into something the agent can call). Everything else in the SDK — guardrails, sessions, handoffs, tracing — attaches to one of these three.

### In Simple Words
Most people expect an SDK this capable to need dozens of core concepts to learn. It doesn't. `Agent` is the *what* — the brain and its available actions. `Runner` is the *go* — it actually runs the reasoning-and-acting loop until the task is done. `@function_tool` is the *bridge* — the thing that lets ordinary Python functions become tools the model can reach for.

### Real Example
The smallest useful agent, fully typed:
```python
from agents import Agent, Runner, function_tool
from agents.result import RunResult

@function_tool
def get_weather(city: str) -> str:
    """Return the current weather for a city. Stubbed for this example."""
    return f"It's 22°C and sunny in {city}."

agent: Agent = Agent(
    name="WeatherBot",
    instructions="You answer weather questions concisely.",
    tools=[get_weather],
)

result: RunResult = Runner.run_sync(agent, "What's the weather in Karachi?")
print(result.final_output)
```
Notice what `result.final_output` actually contains: not the raw string `"It's 22°C and sunny in Karachi."`, but a model-composed rewrite of it. The model calls the tool (one model call), reads the result, then writes the final answer in its own voice (a second model call) — roughly two model calls per tool invocation is a useful rule of thumb for estimating cost.

### The Pitfall
Expecting the SDK to have 5–7 core primitives and getting lost trying to track them all. Most learners overshoot this guess. There are three. Guardrails, sessions, handoffs, and tracing are modifiers *of* `Agent` or `Runner` — not a fourth, fifth, and sixth foundational concept to learn from scratch.

### Guiding Question (Answer This From Memory Later)
What are the three primitives that appear in every OpenAI Agents SDK codebase?

---

## Function Tools: The Model's Callable Contract

### The Idea
`@function_tool` inspects a Python function's type hints and docstring and turns them into the JSON schema the model reads before deciding whether and how to call it. The type hints aren't just documentation — the SDK validates the model's arguments against them *before* your function body ever runs.

### In Simple Words
Write the docstring exactly the way you'd describe the tool to a new colleague — that's precisely what the model reads to decide when and how to use it. The type hints are the tool's shape: if the model tries to hand you a number where you specified text, the SDK catches the mismatch and sends the model back to try again — your function never sees the wrong type.

### Real Example
```python
from typing import Literal
from agents import function_tool

@function_tool
def book_meeting(
    attendee_email: str,
    duration_minutes: Literal[15, 30, 60],
    topic: str,
) -> str:
    """Schedule a meeting on the user's calendar.

    Use only after the user has confirmed both the time and the
    attendee. Do not call this to look up availability.
    """
    return f"Booked {duration_minutes} min with {attendee_email}: '{topic}'."
```
If a user asks for a 45-minute meeting, `Literal[15, 30, 60]` makes that request impossible to pass through as an argument — a real guardrail with zero runtime cost. And the docstring's "use only after..." line prevents the model from calling `book_meeting` mid-availability-check, which is the single most common bug in calendar agents.

### The Pitfall
Writing a technically correct but vague docstring ("books a meeting") with no guidance on *when not* to call it. The model reads the docstring the same way a new colleague would — omit the boundary conditions and it will confidently call the tool at the wrong moment, not out of malice, just because nobody told it not to.

### Guiding Question (Answer This From Memory Later)
What makes a function tool safe to hand to a model?

---

## Context Objects: Trusted State the Model Can't Fake

### The Idea
A context object carries state a tool needs — like a verified user ID — that comes from your application, not from the model's own output. This matters because the model must never be the source of truth for identity or other sensitive per-run state: if a tool argument could set `user_id`, a confused or manipulated model could read or write the wrong person's data.

### In Simple Words
Picture an AI as a hotel concierge running errands for a guest. When the concierge tells the front desk "room 412 wants their mail," the desk must not hand it over on his say-so — a confused concierge could name the wrong room and leak a stranger's mail. The fix: the desk reads who the guest actually is from a passport it already trusts, never from anything the concierge says. A context object is that passport — verified state your code supplies directly, kept separate from whatever the model puts in its tool call arguments.

### Real Example
The rule in code terms: if a tool ever takes something like a `user_id` argument, and the model could fill it with the wrong value, your server must ignore that argument and read identity from a trusted, verified source instead — typically something your application already authenticated, passed in as context, not typed by the model:
```python
# WRONG — the model supplies the identity; a manipulated model could pass someone else's id
@function_tool
def get_user_notes(user_id: str) -> list[str]:
    return db.notes.find(user_id=user_id)   # trusts whatever the model sent

# RIGHT — identity comes from verified context, never from a model-controlled argument
async def get_user_notes(ctx: RunContextWrapper[AppContext]) -> list[str]:
    verified_user_id = ctx.context.user_id   # set by YOUR code before the run starts, not by the model
    return db.notes.find(user_id=verified_user_id)
```
The context object is constructed by your application before `Runner.run` is ever called, carrying only what your code decided to put there — the model can read it inside tool logic but never supplies or overwrites it.

### The Pitfall
Trusting a model-supplied argument for anything that determines *whose* data gets touched. This is the textbook trust bug in agent applications: it works fine in every test where the model behaves as expected, and fails exactly when someone (accidentally or deliberately) gets the model to name the wrong identity.

### Guiding Question (Answer This From Memory Later)
Why does an agent need a context object separate from what the model can see and control?

---

## Agents as Tools & Multi-Agent Orchestration

### The Idea
`Agent.as_tool()` converts one agent into a callable tool another agent can invoke — the coordinator stays in charge, calling specialists the way it would call any function tool. `handoff()` instead transfers the conversation entirely to another agent, which takes over. Use `as_tool()` for hierarchical composition where the coordinator collects and combines results; use `handoff()` when the specialist should genuinely take over.

### In Simple Words
`as_tool()` is a manager calling a specialist consultant, getting their report back, and deciding what to do next — the manager never leaves the room. `handoff()` is transferring the customer to that specialist directly — the manager steps out, and the specialist now runs the conversation.

### Real Example
```python
from agents import Agent, Runner

researcher = Agent(name="researcher", instructions="...", tools=[web_search])
writer = Agent(name="writer", instructions="...", tools=[draft_document])
reviewer = Agent(name="reviewer", instructions="...", tools=[fact_check])

coordinator = Agent(
    name="coordinator",
    instructions=(
        "Decompose the task into research, writing, and review phases. "
        "Use the specialist tools in order. Compose their outputs into a final report."
    ),
    tools=[
        researcher.as_tool(tool_name="research_topic", tool_description="Investigate a topic and return a brief"),
        writer.as_tool(tool_name="draft_document", tool_description="Draft a document from research notes"),
        reviewer.as_tool(tool_name="review_document", tool_description="Review a draft and return critique"),
    ],
)

result = await Runner.run(coordinator, "Write a short report on renewable energy trends")
```
The coordinator stays in control throughout — it calls `research_topic`, reads the result, calls `draft_document`, reads that, calls `review_document`, and composes the final answer itself. Nothing here transfers the conversation away from the coordinator.

### The Pitfall
Reaching for `handoff()` when what you actually want is `as_tool()` (or the reverse). If the coordinator needs to see and combine every specialist's output before responding, `handoff()` is the wrong primitive — it hands away control, and the coordinator never gets to synthesize anything. Match the primitive to whether you need the coordinator to stay involved.

### Guiding Question (Answer This From Memory Later)
What's the difference between `Agent.as_tool()` and `handoff()` for combining agents?

---

## Summary

- **Three Primitives**: `Agent` (brain + tools), `Runner` (the loop), `@function_tool` (the bridge) — everything else in the SDK modifies one of these three.
- **Function Tools**: type hints and docstrings become the model's contract; the SDK validates arguments before your function body runs, and the docstring must state when *not* to call the tool.
- **Context Objects**: verified state (like identity) must come from your application, never from a model-controlled argument — the same trust-boundary discipline from Lesson 8's security review, now applied to agent design.
- **Agents as Tools vs. Handoff**: `as_tool()` keeps the coordinator in control for hierarchical composition; `handoff()` transfers the conversation entirely to a specialist.
- **Why Together**: this is the first hands-on build — you now have the vocabulary from Lessons 9–10 turned into working code: an agent with tools, safe from model-supplied identity, capable of coordinating specialists.

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: Name the SDK's three core primitives and what each one is responsible for.
**Question 2**: Why does the SDK validate a tool's arguments against its type hints *before* the function body runs, rather than letting the function handle bad input itself?
**Question 3**: If a coordinator needs to combine the outputs of three specialist agents into one final report, should it use `as_tool()` or `handoff()` for each specialist, and why?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector), Build AI Agents crash course (the SDK in three primitives, hello_agent.py, function tool type validation); Choosing Agentic Architectures crash course (Agent.as_tool() vs. handoff(), multi-agent specialist composition); Connector-Native Apps crash course (identity from verified context, never from the model); Building Agent Factories — Chapter 62: OpenAI Agents SDK (SDK Setup & First Agent, Function Tools & Context Objects, Agents as Tools and Multi-Agent Orchestration).
