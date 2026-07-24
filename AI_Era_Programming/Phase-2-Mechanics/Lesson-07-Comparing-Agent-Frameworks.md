# Lesson 14: Comparing Agent Frameworks — Google ADK, Claude API, and Claude Agent SDK

## Purpose

You've built real agents on the OpenAI Agents SDK. This closing lesson proves the architecture you learned transfers: the same concepts, expressed through three other frameworks, so you can pick the right tool for a job instead of defaulting to the first one you learned.

## Guiding Questions

1. How does Google's Agent Development Kit (ADK) map onto the same primitives you already know from the OpenAI Agents SDK?
2. What's the one capability ADK genuinely lacks compared to the OpenAI Agents SDK, and how do you work around it?
3. Why did Anthropic rename the "Claude Code SDK" to the "Claude Agent SDK"?

---

## Google ADK: The Same Architecture, Different Library

### The Idea
Google's Agent Development Kit (ADK) uses the same conceptual architecture as the OpenAI Agents SDK — an `Agent`, a tool decorator, a runner that drives the loop — just with different names and import paths. Learning a second framework after the first is mostly a vocabulary exercise, not a new architecture to learn from scratch.

### In Simple Words
If you've learned to drive one car, learning a second brand mostly means finding where the wipers and turn signals are — steering, braking, and accelerating all work the same way. ADK and the OpenAI Agents SDK are the same kind of vehicle with different control layouts.

### Real Example
Agent Factory's own side-by-side comparison, rebuilding the exact same agent on both frameworks:

| OpenAI Agents SDK | Google ADK | Same concept |
|---|---|---|
| `from agents import Agent, function_tool` | `from google.adk import Agent` + `from google.adk.tools import function_tool` | Agent runtime |
| `@function_tool` | `@function_tool` | Tool decorator |
| `RunContextWrapper` | `context={...}` kwarg to `run_async` | Per-run state |
| `Runner.run(agent, ...)` | `agent.run_async(...)` | Agent execution |

The architecture survives the library swap intact — only the imports and a few call shapes change. That's the real test of whether you learned the *pattern* or just memorized one SDK's syntax: if the concepts (agent, tool, context, runner) transfer cleanly to a second framework, you learned the pattern.

### The Pitfall
Assuming every capability maps one-to-one. It doesn't — see the next concept for the one place ADK genuinely diverges.

### Guiding Question (Answer This From Memory Later)
How does Google's Agent Development Kit (ADK) map onto the same primitives you already know from the OpenAI Agents SDK?

---

## Where ADK Genuinely Differs: No First-Class Tool Guardrail

### The Idea
As of Agent Factory's writing, Google ADK has no direct equivalent to the OpenAI Agents SDK's `tool_input_guardrail` decorator — the pre-execution check on a specific tool call's arguments that Lesson 12 covered. The workaround is validating inside the tool body itself, backed by whatever external safety net (like spend caps on a wallet) is available.

### In Simple Words
The OpenAI Agents SDK gives you a security guard stationed right at the tool's door, checking IDs before anyone gets in. ADK doesn't have that exact guard post — so the check happens just inside the door instead, by the person already at the desk. It's a real capability difference, not just a naming difference, and it's worth knowing which trade-off you're accepting when you pick a framework.

### Real Example
```python
# OpenAI Agents SDK: dedicated pre-execution guardrail primitive
@tool_input_guardrail
def block_secret_args(data: ToolInputGuardrailData) -> ToolGuardrailFunctionOutput:
    if "sk-" in data.context.tool_arguments:
        return ToolGuardrailFunctionOutput.reject_content("Argument looks like a secret.")
    return ToolGuardrailFunctionOutput.allow()

# Google ADK: no direct equivalent — the check moves in-tool, backed by an external safety net
@function_tool
async def x402_paid_fetch(url: str, max_payment_usdc: Decimal) -> X402PaymentResult:
    """Fetch a URL that may need x402 payment up to max_payment_usdc."""
    # ADK has no tool_input_guardrail, so the check runs in-tool,
    # backed by the wallet's on-chain spend cap — the layer nothing can bypass.
    resp = await x402_client.get(url, max_payment_usdc=max_payment_usdc)
    return X402PaymentResult(content=resp.content, amount_paid_usdc=resp.amount_paid_usdc)
```
The external safety net (an on-chain spend cap, in this case) still protects you either way — the in-tool check just fails a step later than a dedicated pre-execution guardrail would. Pick ADK and you're trading that guardrail convenience for whatever ADK offers instead (Agent Factory notes its mandate-signing flows are more native, for instance).

### The Pitfall
Choosing a framework purely by familiarity and only discovering a capability gap once you're deep into a build. Knowing this specific trade-off *before* you commit — guardrail convenience vs. other ecosystem strengths — is exactly the kind of informed choice Lesson 10's "picking your engine" framework was training you to make.

### Guiding Question (Answer This From Memory Later)
What's the one capability ADK genuinely lacks compared to the OpenAI Agents SDK, and how do you work around it?

---

## The Claude Agent SDK: Beyond Coding

### The Idea
Anthropic renamed its underlying framework from "Claude Code SDK" to the "Claude Agent SDK" because teams were using it for far more than writing code — research, video production, data analysis, note-taking, and dozens of other non-coding tasks. The rename reflects what the tool actually is: a general-purpose agent framework, not a coding-specific one.

### In Simple Words
Imagine a tool named "the hammer" that everyone kept using to also drive screws, pry boards, and measure angles — eventually you'd rename it to "the toolkit," because the name should describe what people actually do with it, not just its first use case. That's what happened here: the tool outgrew its original name.

### Real Example
Claude Code (built on the Claude Agent SDK) reads your files, runs commands, manages code, and can delegate subtasks to specialized helpers working in parallel — the same operational shape as any agent you've built in this course: instructions, tools, a loop, and the ability to hand off focused work. Its skills system (`SKILL.md` files) and ability to spawn sub-agents are the same conceptual building blocks as the OpenAI Agents SDK's tools and handoffs, just packaged for a general-purpose agent rather than a narrowly-scoped one.

### The Pitfall
Assuming "Claude Agent SDK" and "OpenAI Agents SDK" are interchangeable names for the same thing because they sound similar. They're built by different vendors with different design philosophies — one grew from a coding tool into a general agent framework, the other was built as an agent framework from the start. The underlying agent *concepts* transfer (as this whole lesson has shown), but the specific APIs, defaults, and ecosystem strengths don't.

### Guiding Question (Answer This From Memory Later)
Why did Anthropic rename the "Claude Code SDK" to the "Claude Agent SDK"?

---

## Summary

- **ADK Maps Cleanly**: `Agent`, `@function_tool`, and a runner all have direct OpenAI Agents SDK counterparts — the architecture transfers, only the syntax changes.
- **The Real Difference**: ADK lacks a first-class tool-input guardrail; the workaround is in-tool validation backed by an external safety net — a genuine trade-off, not just a naming quirk.
- **Claude Agent SDK**: renamed from "Claude Code SDK" because it outgrew coding-only use — same agent concepts (tools, delegation, loops), general-purpose framing.
- **Why Together**: this closes the Custom Agent Development track — you now know the architecture well enough to recognize it across frameworks, and can make an informed, trade-off-aware choice instead of defaulting to whichever SDK you happened to learn first.

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: In the OpenAI-SDK-to-ADK comparison, what's the ADK equivalent of `Runner.run(agent, ...)`?
**Question 2**: If you pick Google ADK for a project that needs strict pre-execution validation on a specific tool's arguments, what's your workaround, and what still protects you?
**Question 3**: What does the "Claude Agent SDK" rename tell you about how the tool's actual usage diverged from its original name?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector), Payment-Enabled Agents crash course — Decision 5 (A non-Stripe, non-OpenAI stack: Google ADK comparison table and guardrail gap); Which AI Employees in 2026 (Claude Code / Claude Agent SDK rename and general-purpose framing); Building Agent Factories — Chapter 63: Building Custom Agents with Google ADK, Chapter 64: The Claude API, Chapter 65: Anthropic Claude Agent SDK.
