# Lesson 9: Agent Architecture Fundamentals

## Purpose

Before you build anything, you need to recognize agent architecture wherever it appears and reason about it precisely. This lesson gives you the vocabulary: what actually separates an agent from a chatbot, the four components every agent needs, the loop that runs underneath all of them, and the patterns for coordinating more than one.

## Guiding Questions

1. What specifically separates an AI agent from a chatbot?
2. What are the four components every agent architecture needs?
3. What is the universal operational loop that appears in every agent system?
4. When do you need a multi-agent system instead of a single agent, and what patterns exist for coordinating one?

---

## What Is an AI Agent?

### The Idea
An AI agent is a system that can independently perceive its environment, make decisions, and take actions toward a goal — without a human guiding every single step. That's the specific line that separates it from a chatbot, which only answers.

### In Simple Words
A chatbot is a librarian standing behind a desk, answering whatever question you bring them. An agent is a personal assistant who takes your request and actually goes out into the world to get it done — checking things, comparing options, taking real actions, coming back with a result instead of just an answer.

### Real Example
> "Find me the cheapest Karachi-to-Dubai flight next Friday."

A chatbot answers with general advice about flight-booking sites. An agent receives this as a goal, then autonomously searches airlines, compares prices, checks your calendar for conflicts, and books the ticket — all without you approving each individual step.

Autonomy itself is a spectrum, not a switch: a junior employee who needs permission for every email has low autonomy; a senior director who decides independently within known boundaries has high autonomy. Agents sit somewhere on this same spectrum — some need human approval for every action, others operate independently within defined boundaries.

### The Pitfall
Calling anything that uses an LLM an "agent." A system that only generates text from a prompt — no tools, no independent action — is a Level 0 system in the taxonomy this course uses, not an agent. The defining trait is autonomous *action* toward a goal, not just language generation.

### Guiding Question (Answer This From Memory Later)
What specifically separates an AI agent from a chatbot?

---

## Core Agent Architecture: The 3+1 Model

### The Idea
Every agent needs four components working together: a **Model** ("Brain," for reasoning), **Tools** ("Hands," for taking action), **Orchestration** ("Nervous System," for planning and sequencing), and **Deployment** ("Body," for making it reachable and accessible).

### In Simple Words
The Brain decides what should happen — "I need to check the customer's order status." The Hands actually do it — calling the order-lookup tool. The Nervous System is what decides the *order* of decisions and actions across a multi-step task, not just one call. The Body is what lets anyone actually reach the agent at all — a chat window, an API, a phone number.

### Real Example
Agent Factory's own Body + Brain framing: your brain decides "I want to pick up that glass" — that's reasoning. Your hand executes the action — that's the body carrying out the decision. In an AI agent, Claude (the Brain) decides "I need to query the database," and the execution layer (the Body) actually runs the query. Miss the Body layer entirely, and a brilliant reasoning model has no way to act on anything or be reached by anyone.

### The Pitfall
Focusing all your design effort on the Model (picking the "smartest" LLM) while treating Tools, Orchestration, and Deployment as afterthoughts. A weaker model with well-designed tools and clear orchestration often outperforms a stronger model with vague, poorly-scoped tools — the architecture around the brain matters as much as the brain itself.

### Guiding Question (Answer This From Memory Later)
What are the four components every agent architecture needs?

---

## The Operational Loop: Get Mission, Scan, Think, Act, Observe

### The Idea
Underneath every agent, regardless of framework, runs the same five-step loop: **Get Mission** (receive the goal) → **Scan Scene** (gather relevant context) → **Think** (reason about what to do) → **Act** (take an action, usually via a tool) → **Observe** (check the result) — then the loop repeats, informed by what was just observed.

### In Simple Words
This is the same loop a competent employee runs on any task: understand what's being asked, look around at the current situation, decide the next move, do it, then check whether it worked before deciding what to do next. Nobody does all the work in one giant leap — it's a repeating cycle of small, checked steps.

### Real Example
A customer support agent handling "where's my order?":
1. **Get Mission**: the customer's message is the goal — resolve their order-status question
2. **Scan Scene**: pull the conversation history, check what account is authenticated
3. **Think**: reason that an order-lookup tool call is needed before anything else
4. **Act**: call `lookup_order(order_id)`
5. **Observe**: read the result — shipped, delayed, or not found — and loop back to Think for what to say or do next

This loop is why agents feel qualitatively different from a single prompt-response: the Observe step feeds directly back into the next Think step, so the agent's next action is genuinely informed by what actually happened, not just what it predicted would happen.

### The Pitfall
Assuming more Think steps automatically means a better agent. A loop that reasons extensively but never actually reaches Act produces the appearance of deliberation with no real progress. The loop only earns its value when Observe genuinely changes what happens next — otherwise it's just an expensive way to restate the same plan.

### Guiding Question (Answer This From Memory Later)
What is the universal operational loop that appears in every agent system?

---

## Multi-Agent Design Patterns

### The Idea
Not every task needs multiple agents, and the choice isn't stylistic — it follows from specific properties of the task. Agent Factory's decision framework: if the solution path can be defined in advance and stays stable, use a **sequential workflow**. If it can't be defined in advance, a **single agent with tools** reasoning step-by-step (ReAct) usually suffices — reach for **multi-agent** only when there's a genuine specialization, context, or scale bottleneck.

### In Simple Words
Don't hire a team when one competent person can do the whole job — that just adds coordination overhead nobody asked for. Multi-agent systems are worth the overhead only when one agent genuinely can't hold everything in its head at once, needs specialized expertise a single generalist can't reasonably carry, or needs to parallelize real, separable work.

### Real Example
Agent Factory's own decision triggers for going multi-agent are quantitative, not vibes-based: over 30% tool-routing errors signals a genuine specialization need; over 10% accuracy drop at higher context signals a context-overflow bottleneck; over 2× latency overrun signals a real scale bottleneck. Absent those signals, a single agent with tools is very likely the right call — and the course names this directly as a common failure: choosing multi-agent for a task (like drafting one piece of content) that a single agent handles fine, paying real coordination cost for no real benefit.

When multi-agent genuinely is warranted, the coordination patterns include:
- **Coordinator**: one agent routes to parallel specialists
- **Sequential**: a pipeline, each agent's output feeding the next
- **Reflection**: an agent (or a second agent) reviews and critiques output before it ships
- **Human-in-the-Loop**: a human approves at a defined checkpoint before anything irreversible happens

### The Pitfall
"Multi-agent for everything" as a default architecture. Agent Factory explicitly names this as the overshoot failure mode — a wrong choice here compounds expensively in production, since every additional agent adds coordination overhead, cost, and new failure surfaces that a correctly-scoped single agent never has to deal with.

### Guiding Question (Answer This From Memory Later)
When do you need a multi-agent system instead of a single agent, and what patterns exist for coordinating one?

---

## Summary

- **Agent vs. Chatbot**: an agent perceives, decides, and *acts* toward a goal autonomously — a chatbot only answers; autonomy itself is a spectrum, not a binary.
- **The 3+1 Architecture**: Model (Brain, reasoning), Tools (Hands, action), Orchestration (Nervous System, sequencing), Deployment (Body, reachability) — all four matter, not just the model.
- **The Operational Loop**: Get Mission → Scan Scene → Think → Act → Observe, repeating, with Observe genuinely informing the next Think step.
- **Multi-Agent Patterns**: default to a single agent; go multi-agent only for a real specialization, context, or scale bottleneck, using Coordinator, Sequential, Reflection, or Human-in-the-Loop patterns as needed.
- **Why Together**: this is the mental model you carry into every framework in the following lessons — before you write a line of OpenAI Agents SDK code, you should already be able to say what architecture you're building and why.

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: What's the one specific capability that separates an agent from a chatbot?
**Question 2**: Name the four components of the 3+1 architecture and what each one is responsible for.
**Question 3**: What quantitative signals justify moving from a single agent to a multi-agent system, according to Agent Factory's decision framework?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector), Building Agent Factories — Chapter 61: Introduction to AI Agents (What Is an AI Agent?, Core Agent Architecture, The Agentic Problem-Solving Process, Multi-Agent Design Patterns); Choosing Agentic Architectures crash course (the five-question decision tree and pattern selection framework); Agent Factory Glossary (Agent, Agentic AI, Autonomy, Multi-Agent System, Body+Brain entries).
