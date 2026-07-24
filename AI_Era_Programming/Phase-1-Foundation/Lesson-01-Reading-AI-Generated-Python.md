# Lesson 1: Reading AI-Generated Python — Method, Principles, and Environment

## Purpose

These 4 concepts are your entry ticket into the entire course. Together they answer one question: before you write a single spec, how do you learn to *read* what AI hands you — and trust your own judgment about it?

## Guiding Questions

1. What is PRIMM-AI+, and why does it start with a guess instead of an answer?
2. What guiding principle sits underneath every AI-driven workflow in this book?
3. What tools make up your workbench, and what does each one actually catch?
4. How do you read Python code you didn't write, without getting lost?

---

## PRIMM-AI+: Learn by Reading, Not by Typing

### The Idea
PRIMM-AI+ is the five-step method this entire book uses to teach programming: **Predict → Run → Investigate → Modify → Make**. You guess what code will do, run it to check, ask why the gap exists, change one thing, then eventually specify your own version and have AI build it.

### In Simple Words
Imagine learning to cook by watching a chef, but before every dish comes out of the oven, you have to guess what it'll taste like. That guess is the whole point — being wrong tells you exactly what you misunderstood about the recipe. Right guesses are nice, but wrong guesses are where the real learning happens. PRIMM-AI+ does that with code: you predict, then you find out, then you ask "why."

### Real Example
From Agent Factory: here's a real prediction exercise from the book's own crash course.

```python
notes = [{"text": "buy milk", "done": True}, {"text": "call mom", "done": False}]
result = [n["text"] for n in notes if n["done"]]
```

**Predict** (before running): what does `result` hold?
**Run**: `result` is `["buy milk"]`.
**Investigate**: you ask the agent, "explain this line as if I've never coded" — that's PRIMM-AI+'s Investigate step, on demand, and Agent Factory calls it *faster than any reference*.
**Modify**: change `n["done"]` to `not n["done"]` and predict again before running.
**Make**: now you specify your own version — "give me a list of unfinished note texts" — and let the agent write it, verifying the output matches your spec.

### The Pitfall
The instinct is to skip straight to Run — just see the answer. Agent Factory is explicit that Predict is *not optional*: a wrong prediction reveals exactly which part of your mental model is broken, and skipping it quietly erodes the skill the whole method is built to protect.

### Guiding Question (Answer This From Memory Later)
What is PRIMM-AI+, and why does it start with a guess instead of an answer?

---

## The Guiding Principle: You Own the First 10% and the Last 10%

### The Idea
AI-Driven Development means AI generates the bulk of the code from your specifications — but the human still owns deciding what "correct" means and verifying that it actually is. Agent Factory frames this as the **10-80-10 rule**: you own the first 10% (intent, direction, what correct looks like), AI handles the middle 80% (writing the code), and you return for the final 10% (verification and judgment).

### In Simple Words
Think of directing a film instead of acting in every scene yourself. You decide what the shot should look like (10%), the crew and actors do the actual filming (80%), and you're back in the editing room deciding what's a keeper and what's a reshoot (10%). You're never absent — you're just not doing the typing.

### Real Example
You want a function that adds tax to a price. Under this principle, your job isn't to type the function — it's to define what "correct" means *before* AI writes anything:

```python
# Your 10%: you decide what correct means, before code exists
def test_total_with_tax():
    assert total_with_tax(100.0, 0.15) == 115.0
    assert total_with_tax(0.0, 0.15) == 0.0
```

AI's 80% is writing `total_with_tax` so that test passes. Your final 10% is running `pytest`, reading the result, and deciding: does this actually handle a negative price? A price of `None`? That judgment call is yours — Agent Factory is explicit that the test is *not* verification of existing code, it's the declaration of what correct means, and delegating that declaration to AI means you have "no independent signal" left to check its work against.

### The Pitfall
Learners often think "AI-driven" means hands-off. It's the opposite — you're more responsible for precision, not less, because vague direction produces 80% of confidently wrong code instead of 80% of correct code. "It looks right" is not verification.

### Guiding Question (Answer This From Memory Later)
What guiding principle sits underneath every AI-driven workflow in this book?

---

## The Development Environment: Your Workbench

### The Idea
Your Python workbench has four tools, each installed and driven through your coding agent rather than typed from memory: **uv** (installs Python and packages), **pytest** (runs your tests), **Pyright** (checks your type annotations before code ever runs), and **Ruff** (checks style and formatting).

### In Simple Words
uv is the workshop that stocks your tools. pytest is the friend who checks your homework against the answer key. Pyright is a proofreader who catches "this doesn't even make sense" before you submit anything. Ruff is a spell-checker for code style. You don't build any of them — you just learn what each one is for.

### Real Example
Say the agent writes this function and then calls it wrong:

```python
def total_with_tax(price: float, tax_rate: float) -> float:
    return price + (price * tax_rate)

total_with_tax("100", 0.15)   # "100" is text, not a number
```

Run Pyright *before* this code ever executes, and it catches the mismatch instantly:

```text
error: Argument of type "Literal['100']" cannot be assigned to
       parameter "price" of type "float"   (reportArgumentType)
```

That's the type checker reading the annotations (`price: float`) and flagging that text was passed where a number was promised — a whole class of mistake caught without running anything.

### The Pitfall
New learners try to memorize `uv` and `pyright` commands. Don't. Agent Factory's own setup instruction to the agent is one sentence — *"Set up a new Python project here using uv. Add pytest, pyright, and ruff"* — and the agent runs every command, asking permission first. Your job is to know what each tool catches, not to type its syntax from memory.

### Guiding Question (Answer This From Memory Later)
What tools make up your workbench, and what does each one actually catch?

---

## Reading Python: Predict-Run-Investigate in Practice

### The Idea
Reading Python fluently means you can trace what a traceback (Python's error report) is telling you, read code bottom-up when it crashes, and recognize patterns (a class, a type hint, a loop) without needing to write them from a blank page yet.

### In Simple Words
A traceback looks like a wall of scary red text, but it's actually a gift: read the very last line first, because that's the real story. Everything above it is just the trail of breadcrumbs showing how the code got there.

### Real Example
```text
Traceback (most recent call last):
  File "tax.py", line 2, in total_with_tax
    return price + (price * tax_rate)
TypeError: can't multiply sequence by non-int of type 'float'
```
Read bottom-up: the last line says a `str` was multiplied where a number was expected — meaning `price` arrived as text (`"100"`) instead of a number (`100.0`). You don't have to fix it yourself. You have to read it well enough to tell the agent precisely: *"the price is arriving as text, not a number. Handle that."* A precise description gets a precise fix.

### The Pitfall
The single most expensive habit in AI-driven coding is hitting "try again" on a failure you didn't actually read. The agent will cheerfully generate a *different* wrong answer. One informed prompt, grounded in a traceback you actually read, beats ten blind re-prompts.

### Guiding Question (Answer This From Memory Later)
How do you read Python code you didn't write, without getting lost?

---

## Summary

- **PRIMM-AI+**: Predict, Run, Investigate, Modify, Make — you learn to read code by guessing first, and the gap between your guess and reality is where the learning happens.
- **The 10-80-10 Rule**: you own defining "correct" (first 10%) and verifying it (last 10%); AI owns generating the code (middle 80%) — this is the principle underneath everything else in the book.
- **The Workbench**: uv installs, pytest tests, Pyright type-checks before code runs, Ruff checks style — four tools you direct, not memorize.
- **Reading Tracebacks**: read bottom-up, the last line is the real story, and a precise description of the error beats a blind re-prompt every time.
- **Why Together**: none of the later phases (specifying types, writing tests, debugging) work if you can't first read what AI hands you and verify it against a standard you set — this lesson is the reading fluency everything else assumes.

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: What are the five steps of PRIMM-AI+, in order, and which step is most often skipped?
**Question 2**: In the 10-80-10 rule, what specifically are the human's two 10% responsibilities?
**Question 3**: If Pyright throws a type error before your code runs, and pytest fails after it runs, what does each tool catch that the other doesn't?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector), Programming in the AI Era — Phase 1: The Workbench (Chapters 42–45); Python in the AI Era crash course (Part 1–4, PRIMM-AI+ method and traceback reading examples).
