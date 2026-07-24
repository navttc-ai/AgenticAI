# Lesson 21: Full Workflow Orchestration and the Shippable Skill Capstone

## Purpose

This is the last MCP lesson. It combines everything — MCP calls, scripts, iteration, error recovery — into one orchestrated workflow, then closes the entire MCP Development track with the capstone that proves you can ship a real, working skill end to end.

## Guiding Questions

1. What does a workflow need to safely combine MCP calls, scripts, and iteration without running unsupervised into a mess?
2. What's the difference between a step failing and a workflow being unsafe to run overnight?
3. What does the Shippable Agent Skill capstone actually require you to demonstrate?

---

## Full Workflow Orchestration: Combining MCP, Scripts, and Iteration

### The Idea
A fully orchestrated workflow combines MCP tool calls, script execution, and iteration into a single pipeline that can run multiple steps, check its own work, and recover from failures — not just one skill doing one thing, but several capabilities working together toward a larger goal.

### In Simple Words
Every earlier lesson taught one instrument — an MCP call here, a script there, a single iteration loop. Full orchestration is the first time they all have to play together: fetch data through MCP, process it with a script, check the result, and loop back if something didn't work — a genuine pipeline, not a single step.

### Real Example
The seven things any workflow needs before it's safe to let run without you watching every step:
- **A success condition** — how it knows the work is actually done
- **A limit** — max tries, minutes, or spend, so it can't run forever
- **Isolation** — parallel work doesn't collide with itself
- **A read-only checker** — something that grades the work but can't silently "fix" it by weakening the check
- **A state file** — so it remembers progress between steps or runs, rather than losing everything on a restart
- **A human gate** — risky or failed work goes to a person, never straight into production
- **A log or notification** — so a failure is visible, not silent

A workflow combining an MCP call (fetch customer data), a script (process and validate it), and a check step (verify the result before proceeding) needs all seven of these to be genuinely safe to run unsupervised — missing even one turns "orchestrated" into "unsupervised and unaccountable."

### The Pitfall
Building the happy-path orchestration (MCP call → script → done) and treating the safety scaffolding as optional polish added later. Missing the limit means a stuck loop runs forever; missing the human gate means a risky action ships without anyone reviewing it. These aren't nice-to-haves layered on top — they're what makes the difference between "orchestrated" and "unsafe to leave running."

### Guiding Question (Answer This From Memory Later)
What does a workflow need to safely combine MCP calls, scripts, and iteration without running unsupervised into a mess?

---

## Error Recovery in a Multi-Step Workflow

### The Idea
In an orchestrated workflow, a single step failing shouldn't mean the whole pipeline is unsafe or has to restart from scratch — but the workflow does need explicit rules for what happens when a step fails: retry it, skip it and flag it, or halt the whole run, depending on what that specific failure means.

### In Simple Words
If one ingredient is missing halfway through cooking a meal, you don't have to throw out everything you've already cooked and start over — but you do need to decide, in that moment, whether to substitute, skip that dish, or stop entirely. A workflow needs the same explicit decision built in ahead of time, not improvised in the moment.

### Real Example
A concrete workflow-recovery pattern: when a scheduled maintenance loop finds real overnight failures, drafts fixes, and has a separate checker agent review each one — three explicit rules govern what happens next: the checker must return an explicit pass before anything proceeds; only low-risk changes are allowed to open a pull request automatically; and anything risky *or* failing gets routed to a "needs a human" flag instead of quietly landing anywhere important. Every run is capped and logged regardless of outcome, so even a fully failed run leaves a visible trace rather than disappearing silently.

### The Pitfall
Treating "the step failed" and "the workflow is broken" as the same thing. A well-designed workflow expects individual steps to fail sometimes — the actual design question is what happens *next*, and leaving that undefined is what turns an ordinary failure into a genuine incident.

### Guiding Question (Answer This From Memory Later)
What's the difference between a step failing and a workflow being unsafe to run overnight?

---

## The Shippable Skill Capstone

### The Idea
The Chapter 68 capstone requires a complete, deployable skill demonstrating the full code execution pattern: an MCP-wrapping skill with proper triggering and a measurable token reduction, a script-execution skill that writes and iterates on real code, and explicit error recovery covering the syntax, runtime, and timeout failures that actually occur in practice.

### In Simple Words
Every earlier concept in this chapter was a rehearsal for one specific skill. The capstone is the first time all of it has to work together in one shippable package — not "I understand MCP-wrapping" and "I understand script execution" as separate facts, but one working artifact that does both, handles its own failures, and is efficient enough to actually be worth using.

### Real Example
The capstone's concrete success criteria: analyze an existing MCP-wrapping skill and explain its intelligence layer; build a skill that wraps an MCP server with proper triggering *and* at least a 30% reduction in tokens compared to exposing the raw server directly; build a skill that writes, executes, and iterates on Python scripts; implement error recovery that specifically handles syntax errors, runtime errors, and timeout errors, not just a generic catch-all; and complete a capstone that is a genuinely shippable skill implementing the full pattern — MCP-wrapping, script execution, and error recovery, together.

### The Pitfall
Building each piece separately and calling the capstone done once all three technically exist. The whole point of "shippable" is that it works as one coherent thing a real user could actually enable and trust — a skill that wraps MCP well but has no error recovery, or recovers from errors but never actually reduces token cost, hasn't met the bar the capstone sets.

### Guiding Question (Answer This From Memory Later)
What does the Shippable Agent Skill capstone actually require you to demonstrate?

---

## Summary

- **Full Orchestration**: combining MCP, scripts, and iteration into one pipeline requires all seven safety pieces (success condition, limit, isolation, checker, state, human gate, logging) — missing any one makes it unsafe to run unsupervised.
- **Error Recovery**: a failed step isn't a broken workflow — the design question is what happens next (retry, skip-and-flag, or halt), decided explicitly ahead of time, not improvised.
- **The Capstone**: a genuinely shippable skill demonstrates MCP-wrapping with measurable token savings, script execution with iteration, and specific (not generic) error recovery, all working together.
- **Why Together**: this closes MCP Development — from understanding what MCP is (Lesson 15) to shipping a complete, orchestrated, self-recovering skill built on top of it (this lesson).

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: Name the seven things a workflow needs before it's safe to run without direct supervision.
**Question 2**: If a workflow step fails, what are the three possible responses a well-designed workflow should choose between?
**Question 3**: What specific, measurable requirement does the capstone set for an MCP-wrapping skill's efficiency?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector), Loop Engineering crash course — Part 5 (the minimum safe loop checklist, the morning-triage recovery example); AI Agent Nervous System crash course — Concept 14 (replay and recovery patterns); Building Agent Factories — Chapter 68: Agent Skills & MCP Code Execution (Full Workflow Orchestration, Capstone: Shippable Agent Skill).

---

**This completes the MCP Development track (Chapters 66–68, Lessons 15–21).**
