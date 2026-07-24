# Lesson 20: Building MCP-Wrapping and Script-Execution Skills

## Purpose

A skill that just describes a task is advice. A skill that orchestrates real execution — calling MCP tools intelligently, or writing and running a script — is autonomy. This lesson covers both patterns and the same build-test-iterate loop that makes either one reliable.

## Guiding Questions

1. What does an MCP-wrapping skill add on top of a raw MCP server that the server alone doesn't provide?
2. What's the practical loop for building and hardening a skill, once the first draft exists?
3. When should a calculation be a small script instead of a prose instruction?
4. What are the two most common skill failure patterns, and how do you fix each?

---

## Anatomy of MCP-Wrapping Skills

### The Idea
An MCP-wrapping skill adds an intelligence layer on top of a raw MCP server — deciding *when* to call it, *how* to filter its results, and *how* to handle its errors — rather than just exposing the server's tools directly and hoping the model figures out the judgment calls itself.

### In Simple Words
A raw MCP server is a library with millions of books and no librarian. An MCP-wrapping skill is the librarian — the same books are all still there, but now something knows which shelf to check first, what to skip because it's outdated, and what to say when the requested book doesn't exist. The books didn't change; the judgment layered on top of them did.

### Real Example
The economic case for wrapping rather than exposing raw: progressive disclosure means an agent pays a small, fixed cost (roughly 100 tokens) just to know a skill exists, and only pays the fuller cost of the skill's body when it's actually activated for a matching task. A well-designed MCP-wrapping skill can filter a large, noisy result set down to exactly what's relevant before it ever reaches the model's context — the same discipline behind the "fetch only relevant chunks" principle from Lesson 13's RAG concept, now applied to filtering an MCP server's raw output rather than a vector search's raw output.

### The Pitfall
Exposing an MCP server's tools directly and assuming the model will apply the same judgment a purpose-built wrapper would. Without the wrapping layer, every call pays the server's full, unfiltered result cost, and every judgment call about *when* to call it and *how* to interpret the response falls entirely on the model's general reasoning — no accumulated, tested logic backing it up.

### Guiding Question (Answer This From Memory Later)
What does an MCP-wrapping skill add on top of a raw MCP server that the server alone doesn't provide?

---

## Building Your Skill: Draft, Test, Iterate

### The Idea
A skill is never done on the first draft. The reliable build loop: describe it and generate a first version, read it and fix anything obviously wrong, test triggering (does it activate on the right phrases and stay quiet on unrelated ones?), test the output on a real input, then test it against hard edge cases — bringing failures back for a targeted fix each time.

### In Simple Words
You wouldn't ship a recipe after tasting it once under perfect conditions. You'd try it again with slightly wrong ingredient amounts, a colder oven, a rushed timeline — the situations that actually happen in practice — and fix the recipe based on what breaks. Building a skill is the same iterative tightening, not a one-shot draft.

### Real Example
The concrete loop, applied to (for instance) a monthly client-summary skill:
1. Describe the skill, let the agent generate a first version.
2. Read it, fix anything obviously wrong before testing.
3. Test triggering — try phrases that *should* activate it ("prepare the client summary," "do the monthly close") and confirm it loads; try unrelated requests and confirm it stays out of the way.
4. Test the output on a real or realistic input — does it format correctly, group things right, catch the details it's supposed to catch?
5. Test hard cases specifically — a client with no activity that month, a value exactly on a threshold, messy input data.
6. Bring failures back with a specific fix request: *"This skill double-counted reversed entries. Update it to net out reversals before grouping."*

### The Pitfall
Testing only the happy path and calling it done. The hard cases (empty input, boundary values, messy real-world data) are exactly where skills silently misbehave — step 5 isn't optional polish, it's where most real bugs actually surface.

### Guiding Question (Answer This From Memory Later)
What's the practical loop for building and hardening a skill, once the first draft exists?

---

## Script Execution Fundamentals: Code Is Exact, Prose Is Interpreted

### The Idea
Some tasks are better handled by a small script the skill writes and runs than by a prose instruction the model interprets fresh every time — because code executes exactly and consistently, while a prose instruction gets *re-interpreted* on every single run, with room for drift.

### In Simple Words
Telling someone "round to the nearest cent, and if it's exactly halfway, round up" in words leaves room for a slightly different reading each time you ask. A script that runs `round(amount, 2)` does the exact same thing every single time, with zero interpretation required — it's the difference between a description of a calculation and the calculation itself.

### Real Example
The concrete signal for reaching for a script instead of prose: when testing a skill against hard cases (per the previous concept) reveals a tricky calculation getting handled inconsistently — a payment exactly on a threshold, reversed entries needing to net out before grouping — the fix isn't a more elaborate paragraph of instructions. It's replacing that specific step with a small script:
```python
# script: net_reversals.py — replaces a fragile prose instruction
def net_out_reversals(entries: list[dict]) -> list[dict]:
    """Cancel matching reversal pairs before grouping, deterministically."""
    seen = {}
    result = []
    for entry in entries:
        key = (entry["amount"], entry["account"])
        if entry.get("is_reversal") and key in seen:
            seen.pop(key)  # cancel the original
            continue
        seen[key] = entry
        result.append(entry)
    return result
```
Once this logic lives in a script, every run nets out reversals identically — no more relying on the model to correctly interpret "net out reversals" fresh each time.

### The Pitfall
Reaching for a script for every calculation, even trivial ones, out of a sense that "code is always more reliable." Scripts add a maintenance surface (a file to keep correct and versioned) that a simple, unambiguous prose instruction doesn't need. Reserve scripts for the specific calculations where testing has actually shown prose-based interpretation drifting or failing — not preemptively for everything.

### Guiding Question (Answer This From Memory Later)
When should a calculation be a small script instead of a prose instruction?

---

## Two Failure Patterns and Their Fixes

### The Idea
Nearly every skill-triggering problem falls into one of two patterns: it never triggers when it should, or it triggers on things it shouldn't. Each has a distinct, specific fix — recognizing which pattern you're looking at tells you exactly where to intervene.

### In Simple Words
A doorbell that never rings when someone's actually there, and a doorbell that goes off every time a leaf blows past, are both broken — but you'd fix them completely differently. One needs a more sensitive trigger; the other needs a less sensitive one. Skill triggering has exactly the same two failure shapes.

### Real Example
| Pattern | Cause | Fix |
|---|---|---|
| It never triggers | The description is too vague, or missing the words people actually use | Add the specific phrases users say, into the "use when..." clause |
| It triggers on the wrong things | The description is too broad | Narrow it, or add an explicit negative trigger ("does not handle...") |

Both fixes live in the description field, not the body — reinforcing Lesson 19's point that the description is what does the triggering work; the body only matters once triggering has already succeeded correctly.

### The Pitfall
Trying to fix an over-triggering skill by editing its body logic instead of its description. If the skill is firing at the wrong moments, the problem is in the *trigger*, not the *execution* — tightening what happens once it's running does nothing to stop it from running when it shouldn't have in the first place.

### Guiding Question (Answer This From Memory Later)
What are the two most common skill failure patterns, and how do you fix each?

---

## Summary

- **MCP-Wrapping Skills**: add judgment (when to call, how to filter, how to handle errors) on top of a raw MCP server — the librarian, not just the library.
- **Build-Test-Iterate**: draft, test triggering on both matching and non-matching phrases, test on real input, then specifically test hard edge cases — most real bugs hide in step 5.
- **Script vs. Prose**: reach for a script when testing reveals a calculation being interpreted inconsistently — code executes exactly; prose gets re-interpreted every run.
- **Two Failure Patterns**: never-triggers (vague description, fix by adding real phrasing) and over-triggers (too-broad description, fix with narrowing or negative scope) — both fixed in the description, not the body.
- **Why Together**: both patterns — wrapping an MCP server intelligently and choosing script over prose — are instances of the same underlying discipline: don't leave to model interpretation what can be made exact and tested instead.

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: What specific value does an MCP-wrapping skill add that calling the raw MCP server directly wouldn't provide?
**Question 2**: In the build-test-iterate loop, why is testing hard cases (step 5) especially important, not just a nice-to-have?
**Question 3**: A skill is triggering on requests it shouldn't handle. Is the fix in the description or the body, and why?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector), Building a Digital FTE crash course — Concept 2 (Progressive Disclosure); Skills & Connectors crash course — Concept 13 (Test, Then Iterate — the build loop and two failure patterns); Building Agent Factories — Chapter 68: Agent Skills & MCP Code Execution (Anatomy of MCP-Wrapping Skills, Build Your MCP-Wrapping Skill, Script Execution Fundamentals, Build Script-Execution Skill).
