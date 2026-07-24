# Lesson 4: Tests as Specification II — Errors, Validation, and Independence

## Purpose

Code that works on the happy path isn't done — it's untested. This lesson covers what happens when things go wrong: how you iterate on AI output instead of re-prompting blindly, how to handle failures gracefully, how to validate data at the boundary, and how to debug well enough to drive the whole cycle without hand-holding.

## Guiding Questions

1. What separates a productive iteration on AI output from a blind re-prompt?
2. What does `try`/`except` actually do, and what's the difference between it and `with`?
3. How does Pydantic go further than a dataclass, and how do you test that it correctly *rejects* bad data?
4. What does it mean to "drive TDG independently," and what's the systematic loop for debugging a real failure?

---

## Iterating on AI Output: Refine, Don't Reprompt Blindly

### The Idea
Iterating on AI output is a disciplined loop: give full context up front, get several options rather than one, give specific feedback about what's wrong and why, and only then move forward — never just hit "try again" and hope for something different.

### In Simple Words
Imagine sending a contractor back to redo work with no notes — just "not quite, try again." They'll guess differently, not better. Now imagine sending them back with "the counter is 2 inches too high, and I wanted matte, not glossy." That's the difference between blind re-prompting and real iteration: specificity is what turns a retry into an improvement.

### Real Example
When a test fails, the unproductive move is re-running the same vague prompt:
> "That didn't work, try again."

The productive move names the actual gap:
> "The test expects `total_with_tax(0.0, 0.15) == 0.0`, but the function returned `15.0` because it's adding tax even on a zero price. Fix the logic so a zero price always returns 0.0."

This same discipline scales to non-code iteration too — Agent Factory's general iteration loop is: load full context → demand 3–5 options, not one → give explicit feedback on what to reject and why → iterate 2–3 times → only then expand the chosen direction into something final. Most of the leverage lives in the loop, not in the first draft.

### The Pitfall
Treating "it failed, try again" as iteration. It isn't — it's a coin flip. Agent Factory's traceback guidance applies directly here: the agent will cheerfully generate a *different* wrong answer to a vague retry. One informed, specific correction beats ten blind ones.

### Guiding Question (Answer This From Memory Later)
What separates a productive iteration on AI output from a blind re-prompt?

---

## Error Handling: try/except and with

### The Idea
`try`/`except` runs risky code and catches specific failures instead of letting the program crash. `with` guarantees that something opened (a file, a connection) gets safely closed, even if an error happens in between.

### In Simple Words
`try`/`except` is a safety net under a tightrope walker: you don't prevent the possibility of a fall, you catch it and decide what happens next instead of letting the whole show stop. `with` is like a self-locking door: whatever's inside gets properly shut and cleaned up when you leave, whether you left calmly or ran out in a panic.

### Real Example
```python
def safe_divide(a: float, b: float) -> float:
    try:
        return a / b
    except ZeroDivisionError:        # if dividing by zero is attempted...
        return 0.0                   # ...do this instead of crashing
```
Predict before running: `safe_divide(10.0, 2.0)` is `5.0`; `safe_divide(10.0, 0.0)` is `0.0` — the exception is caught and handled instead of stopping the program. Read `try`/`except` as *"try this; if it fails in this specific way, do that instead."*

```python
with open("notes.txt") as file:
    contents: str = file.read()
# the file is automatically closed here, even if an error happened above
```
Read `with` as *"set something up, use it inside this block, and tear it down safely no matter what."*

### The Pitfall
Catching an exception too broadly (a bare `except:` that swallows *everything*) hides real bugs instead of handling a specific, anticipated failure. Catch the specific error you expect (`ZeroDivisionError`, `KeyError`) — not "anything that might go wrong."

### Guiding Question (Answer This From Memory Later)
What does `try`/`except` actually do, and what's the difference between it and `with`?

---

## Validation with Pydantic: Rejecting Bad Data at the Boundary

### The Idea
A dataclass gives data shape at development time; Pydantic's `BaseModel` goes further and *validates* data at runtime, rejecting anything that doesn't fit the moment it's constructed — and you test that rejection explicitly with `pytest.raises`.

### In Simple Words
A dataclass is a form with labeled fields — but nothing stops you handing it a nonsense value. Pydantic is that same form with a strict clerk standing over it, who refuses to accept the form at all if a field is out of range. And just like you test that valid data works, Agent Factory is explicit that you also test that bad data gets *refused* — because validation that looks strict but silently lets bad data through is exactly the kind of bug AI-generated code can quietly introduce.

### Real Example
```python
from pydantic import BaseModel, Field

class ModelConfig(BaseModel):
    learning_rate: float = Field(gt=0.0, lt=1.0)   # must be between 0 and 1
    batch_size: int = Field(gt=0)                   # must be positive
```
If you try to build `ModelConfig(learning_rate=-0.05, batch_size=32)`, Pydantic raises a clear error *immediately* — instead of letting a broken value poison a training run hours later.

You pin that rejection down with a test of its own:
```python
import pytest
from pydantic import ValidationError

def test_rejects_negative_learning_rate() -> None:
    with pytest.raises(ValidationError):   # the block below MUST raise this error
        ModelConfig(learning_rate=-0.05, batch_size=32)
```
This test passes *only* if the bad config is rejected — testing failure cases matters as much as testing success cases.

### The Pitfall
Only ever testing the happy path (valid data goes in, valid object comes out) and never testing that invalid data is actually refused. A `Field` constraint that looks correct but was typo'd (`lt=100` instead of `lt=1`) will pass every happy-path test and still let broken data through — only a `pytest.raises` test catches that.

### Guiding Question (Answer This From Memory Later)
How does Pydantic go further than a dataclass, and how do you test that it correctly *rejects* bad data?

---

## Debugging & Driving TDG Independently

### The Idea
Debugging AI-generated code means reading a traceback bottom-up, recognizing the handful of error categories that cover almost everything, and describing the problem precisely rather than fixing it yourself. "TDG independence" means you can run the whole specify → test → generate → verify → debug cycle from a one-sentence problem statement, with no scaffolding or step-by-step hand-holding.

### In Simple Words
A traceback looks like a wall of scary red text, but it's a gift, not a threat: read the very last line first, because that's the whole story. You don't need to be the one who fixes the bug — you need to be the one who names it precisely enough that the fix is obvious to whoever (or whatever) writes it next.

### Real Example
```text
Traceback (most recent call last):
  File "tax.py", line 2, in total_with_tax
    return price + (price * tax_rate)
TypeError: can't multiply sequence by non-int of type 'float'
```
Read bottom-up: a `str` was multiplied where a number was expected. You tell the agent precisely: *"the price is arriving as text, not a number. Handle that."*

Six error categories cover almost everything you'll meet:

| The last line says… | What it usually means | What to tell the agent |
|---|---|---|
| `ModuleNotFoundError` | A package isn't installed, or misspelled | *"X isn't installed — add it with uv"* |
| `NameError` | A name used but never defined | *"y is used but never defined — typo?"* |
| `TypeError` | Wrong kind of value (text where a number expected) | *"a number is arriving as text — convert it"* |
| `AttributeError` | Object asked for something it doesn't have | *"no attribute 'titel' — likely a typo"* |
| `KeyError` | dict asked for a key that isn't in it | *"key 'x' isn't in the dict — handle missing case"* |
| `IndexError` | list asked for a position past its end | *"list is shorter than the code assumes"* |

Driving TDG independently means running this whole loop yourself, from a bare problem statement, with no one telling you what step comes next: specify → write failing tests → generate → run → read failures using this table → refine → verify green.

### The Pitfall
Memorizing fixes for each error type instead of recognizing the category. You're not meant to know the fix from memory — you're meant to recognize *which kind* of problem it is from the last line, and describe that precisely. A precise name gets a precise fix; "it broke" gets another guess.

### Guiding Question (Answer This From Memory Later)
What does it mean to "drive TDG independently," and what's the systematic loop for debugging a real failure?

---

## Summary

- **Iterating on AI Output**: give full context, ask for options, give specific feedback on what's wrong and why — never just "try again" on a failure you haven't diagnosed.
- **Error Handling**: `try`/`except` catches a specific anticipated failure instead of crashing; `with` guarantees safe cleanup no matter what happens inside the block.
- **Pydantic Validation**: `BaseModel` with `Field` constraints rejects bad data the instant it's constructed — and you must test that rejection explicitly with `pytest.raises`, not just test the happy path.
- **Debugging & Independence**: read tracebacks bottom-up, recognize the error category (six cover almost everything), describe it precisely, and run the full TDG loop yourself without scaffolding.
- **Why Together**: this is where verification stops being optional-when-convenient and becomes complete — you now handle failure gracefully, validate at the boundary, and debug systematically enough to run the entire cycle unsupervised.

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: What's the concrete difference between a blind re-prompt and a real iteration on failed AI output?
**Question 2**: Why must you write a `pytest.raises` test for a Pydantic model, not just a test that valid data works?
**Question 3**: If a traceback ends in `AttributeError`, what category of mistake does that usually signal, and what would you tell the agent?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector), Programming in the AI Era — Phase 3b: Iterating on AI Output, Error Handling and Exceptions, Validation with Pydantic (Chapters 53–55), Phase 4: Debug & Master (Chapters 56–57); Python in the AI Era crash course (Part 2–4, try/except, with, Pydantic, and traceback examples); AI Prompting in 2026 (the brainstorm-iterate loop).
