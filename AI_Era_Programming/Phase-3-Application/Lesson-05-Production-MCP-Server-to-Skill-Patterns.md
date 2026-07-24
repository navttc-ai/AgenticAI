# Lesson 19: From Production MCP Server to Advanced Skill Patterns

## Purpose

This lesson closes the MCP server chapter with its capstone, then opens the next one: skills that don't just advise an agent, but orchestrate real execution. The bridge is deliberate — you'll see the same "narrow, well-described, composable" discipline apply to both.

## Guiding Questions

1. What does a production-ready custom MCP server capstone actually require, beyond "the tools work"?
2. What makes a Skill's `description` field the most important line in the whole file?
3. When should you write one large skill instead of several small, chained ones?

---

## The Production MCP Server Capstone

### The Idea
A production MCP server capstone isn't just "tools that work" — it's a narrowly-scoped server (a handful of well-described tools, nothing more), built on the production transport, wired into a real agent, and proven end-to-end on an actual request rather than a toy example.

### In Simple Words
The difference between a demo server and a production one is the same as the difference between a recipe you've read and a meal you've actually cooked and served to someone. The capstone's job is to prove the whole chain works — scope, transport, wiring, and a real result — not just that each ingredient exists somewhere.

### Real Example
A representative capstone shape from Agent Factory's own worked example — a `customer-data` MCP server with exactly three tools, nothing else:
```
/mcp-builder Let's design a custom MCP server called "customer-data"
on the streamable-HTTP transport, stateless flavor. Plan the
implementation first, then build it.

Scope: exactly three tools, nothing else.
- lookup_customer(customer_id): return id, email, tier, open-ticket count
- find_similar_resolved_tickets(description, limit): semantic search over
  past resolved tickets
- issue_refund(order_id, amount_cents, reason): issue a refund AND write
  an audit row in the same transaction

No general SQL tool. Each tool gets a clear description so the model
knows when to call it.
```
The check that proves the capstone actually works: start the server, then run the real worker on a real message ("I'm Sam, and I haven't had my refund for order #4429 in two weeks") and confirm it calls `find_similar_resolved_tickets` and returns ranked past cases — not an empty result, and not a made-up answer. That's the MCP wire genuinely working end to end.

### The Pitfall
Declaring the capstone "done" once the server starts without error. Two concrete red flags Agent Factory names explicitly: a general SQL-style tool showing up means the worker is still wired to a dev-only admin server at runtime (a scope leak); an empty search result means the underlying data seed didn't land in the shape the server expects. Either one means the wiring isn't actually proven yet, even if nothing crashed.

### Guiding Question (Answer This From Memory Later)
What does a production-ready custom MCP server capstone actually require, beyond "the tools work"?

---

## Advanced Skill Patterns: The Description Does the Loading Work

### The Idea
A Skill's `description` field is what an agent reads to decide *when* to load it — not the body, which only loads once triggered. That means the description must be specific enough to recognize the right moment, including explicit negative scope (what the skill does *not* do), not just a general topic summary.

### In Simple Words
Think of the description as a sign on a door, and the body as what's actually inside the room. The agent only reads the sign before deciding whether to walk in — so a vague sign ("stuff about reports") gets ignored or misused, while a precise one ("use when the user wants a weekly customer-health summary; does not handle one-off ad-hoc queries") tells the agent exactly when this is the right room.

### Real Example
The concrete discipline, from Agent Factory's own skill library:
- **"Use when…" clauses carry the real specificity** — "use when user wants to stress-test a plan, get grilled on their design, or mentions 'grill me'" is far more useful to the agent than "for grilling."
- **Skills name their boundaries explicitly** — one skill's description states it "does not interview again," another states it "does not modify the codebase." These negative clauses are what let multiple skills coexist without stepping on each other.
- **Skills name their pairings** — a skill can be implicitly paired with another it hands off to, forming a chain rather than a single monolithic instruction.
- **The body speaks directly, second person** — "Interview me relentlessly," "Ask the questions one at a time" — direct and declarative, the same tone you'd use briefing a junior collaborator, not a formal specification document.

### The Pitfall
Writing a description that describes the skill's *topic* instead of the *trigger moment*. "This skill helps with customer reports" tells the agent what the skill is about but not when to reach for it — the fix is naming the specific situation ("use when...") and what falls outside its scope, not just the subject matter.

### Guiding Question (Answer This From Memory Later)
What makes a Skill's `description` field the most important line in the whole file?

---

## Skill Composition: One Big Skill vs. Several Small Ones

### The Idea
A multi-step job like "produce a weekly customer-health report" can be one skill that does everything in one context, or several small skills that hand off through the filesystem. Both work, with opposite trade-offs — the choice depends on whether steps are reused alone and whether clean context matters more than simple wiring.

### In Simple Words
One big skill is one long errand run by a single person start to finish — efficient if nothing goes wrong partway, but if step three fails, that person is stuck mid-errand with a cluttered mental list of everything before it. Several small skills are a relay team — each runner starts fresh, a dropped baton only affects that one leg, and any single runner can be swapped out or reused in a different race.

### Real Example
The real trade-off, spelled out:
- **One big skill**: easy to discover, one activation — but every step runs in the same context, nothing is independently reusable, and a mid-way failure leaves the model recovering with stale, cluttered context.
- **Several small skills**: each step can be tested, replaced, and reused on its own; a failure stays localized; each step activates fresh, so no leftover context piles up — at the cost of more discovery entries and something that has to chain them together (typically through the filesystem: skill A writes `tmp/research-{id}.md`, skill B reads it and writes `tmp/draft-{id}.md`).

The rule of thumb: write one skill when the steps are tightly coupled and never reused alone; write several when a step might be called independently, or when clean per-step context matters more than simple wiring. Separation usually wins past two or three steps.

### The Pitfall
Defaulting to one giant skill because it feels simpler to build. That simplicity is real at first, but it means any failure partway through leaves the model working from a messy, half-completed context — exactly the situation several small, independently-testable skills were designed to avoid.

### Guiding Question (Answer This From Memory Later)
When should you write one large skill instead of several small, chained ones?

---

## Summary

- **Production MCP Server Capstone**: scoped tools, production transport, real end-to-end proof on an actual message — not just "it starts without error."
- **Advanced Skill Patterns**: the `description` is what triggers loading, so it needs "use when..." specificity and explicit negative scope, not a topic summary.
- **Skill Composition**: one skill for tightly-coupled, never-independently-reused steps; several small chained skills once reuse or clean context matters more than simple wiring.
- **Why Together**: both MCP servers and Skills share the same underlying discipline — narrow scope, precise description, and composability over one giant do-everything unit.

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: Name two concrete red flags that mean an MCP server capstone isn't actually proven working, even if it started without errors.
**Question 2**: What's the difference between a skill description that names a topic and one that names a trigger moment?
**Question 3**: For a 5-step "customer-health report" workflow, would Agent Factory's own rule of thumb favor one skill or several — and why?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector), Building a Digital FTE crash course — Decision 6 (Production MCP server capstone requirements), Concept 5 (Composing Skills, one big vs. several small); Agentic Engineering crash course — Skills as Encoded Process (description-as-trigger discipline, negative scope); Building Agent Factories — Chapter 67 Capstone (Production MCP Server), Chapter 68: Agent Skills & MCP Code Execution (Advanced Skill Patterns, Skill Composition & Multi-Skill Workflows).
