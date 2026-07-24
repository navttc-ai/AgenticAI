# Lesson 2: Your First TDG Cycle & the Type Vocabulary

## Purpose

You can now read code. This lesson gives you the words to *specify* it: the four building-block types, the containers that hold real data, and the loop that turns a failing test into working, verified code — Test-Driven Generation.

## Guiding Questions

1. What is the Test-Driven Generation (TDG) cycle, and why must the test come from you, not the agent?
2. What are Python's four primitive types, and why does every value need one?
3. What do `list`, `dict`, `set`, and `tuple` each promise about the data inside them?
4. Why is a function's type-annotated signature a contract, not just documentation?

---

## Test-Driven Generation: Write the Test First

### The Idea
TDG inverts the old order of "write code, maybe test it." Instead: **write a failing test that defines "correct" → let the agent generate code → run the test to verify.** The test isn't a check you bolt on afterward — it *is* the specification, and it has to come from you.

### In Simple Words
Imagine hiring someone to bake a cake, but instead of describing the cake in words ("kind of sweet, I guess"), you hand them a checklist: exactly this tall, exactly this sweet, must taste like vanilla. They bake; you check the cake against the list. If they wrote the checklist themselves, checking their cake against their own list proves nothing — you need the list to come from someone with no reason to grade their own homework easy.

### Real Example
From Agent Factory: here's a real TDG cycle on a tax function.

**Step 1 — you write the failing test first** (`test_tax.py`):
```python
from tax import total_with_tax

def test_basic() -> None:
    assert total_with_tax(100.0, 0.15) == 115.0

def test_zero_price() -> None:
    assert total_with_tax(0.0, 0.15) == 0.0
```
Run `pytest` now — it fails, because `tax.py` doesn't exist yet. That failure is the point: you have a precise, executable definition of "done."

**Step 2 — the agent generates the implementation.** You instruct it: *"Read test_tax.py. Create tax.py with a total_with_tax(price: float, tax_rate: float) -> float function that makes both tests pass. Then run pytest and show me the result."*

**Step 3 — you verify.** Two green checks (`2 passed`) means the code meets the spec *you* defined. Agent Factory flags one thing to watch closely: if the agent makes a failing test pass by editing the *test* instead of the *code*, stop it — that hides the bug instead of fixing it.

### The Pitfall
Letting the agent write both the code and the test. If AI writes both, you're checking AI's work against AI's own expectations — no independent signal at all. The test must come from a human mind that understands the domain.

### Guiding Question (Answer This From Memory Later)
What is the Test-Driven Generation (TDG) cycle, and why must the test come from you, not the agent?

---

## Primitive Types: The Four Boxes Every Value Fits In

### The Idea
A variable is a labeled box holding a value, and every value has a type. For now there are four to recognize: `str` (text), `int` (whole number), `float` (decimal number), and `bool` (True/False only).

### In Simple Words
Think of four different shaped containers — a jar only for text, a jar only for whole numbers, one for numbers with decimals, one that can only ever say yes or no. Writing `name: str` next to a variable is just labeling the jar so anyone (you or the agent) can see at a glance what's supposed to go inside.

### Real Example
```python
name: str = "Ayesha"        # str   — text, always in quotes
age: int = 30               # int   — a whole number
price: float = 19.99        # float — a number with a decimal point
is_active: bool = True      # bool  — only ever True or False
```
The `: str`, `: int`, `: float`, `: bool` are type hints. They're optional in Python, but Agent Factory uses them everywhere — partly because they make code easier to read, and partly because they're how you tell the agent exactly what to build.

**Predict before running:**
```python
age: int = 30
age = age + 1
print(age)
```
(`age` becomes `31` — the `=` means "put the value on the right into the box on the left," not mathematical equality.)

### The Pitfall
Treating `=` as math-class "equals." In Python, `age = age + 1` isn't a claim that age equals age-plus-one — it's an instruction: compute the right side, then store the result back into the box on the left.

### Guiding Question (Answer This From Memory Later)
What are Python's four primitive types, and why does every value need one?

---

## Collections: Grouping Data Into Containers

### The Idea
Single values are rare in real code — most data comes in groups. Four containers cover almost everything: `list` (ordered, can change), `dict` (labeled key-value pairs), `set` (unique items, no duplicates), and `tuple` (ordered, cannot change).

### In Simple Words
A `list` is a numbered row of lockers you can rearrange. A `dict` is a row of lockers with name tags instead of numbers — you grab things by label, not position. A `set` is a bag that refuses to hold two of the same item. A `tuple` is a locked row you can look into but never rearrange.

### Real Example
```python
notes: list[str] = ["buy milk", "call Sara", "finish report"]      # list  — ordered, can change
note: dict[str, str | bool] = {"title": "Meeting", "done": False}  # dict  — labeled pairs
tags: set[str] = {"work", "urgent"}                                 # set   — unique items
point: tuple[int, int] = (3, 5)                                     # tuple — ordered, fixed
```
`dict[str, str | bool]` reads as "a dict whose keys are text, and whose values are either text or True/False" — the `|` means "or." The two you'll meet constantly are `list` and `dict`; `dict` in particular is the backbone of almost everything an AI agent passes around, because it maps directly to JSON.

**Predict, then run:**
```python
note: dict[str, str | bool] = {"title": "Meeting", "done": False}
print(note["title"])
note["done"] = True
print(note)
```
Output: `Meeting` then `{'title': 'Meeting', 'done': True}`.

### The Pitfall
Assuming you need to memorize how to *write* these type hints from scratch. Agent Factory is explicit: you need to *read* `list[str]` and `dict[str, str | bool]` fluently, not produce them from a blank page yet — that comes later, once reading is automatic.

### Guiding Question (Answer This From Memory Later)
What do `list`, `dict`, `set`, and `tuple` each promise about the data inside them?

---

## Functions as Contracts

### The Idea
A function's type-annotated signature — its name, parameter types, and return type — isn't decoration. It's a contract: it tells both humans and AI exactly what the function accepts and promises to return, and (in agent contexts) it's literally how a model knows what it's allowed to pass.

### In Simple Words
A function signature is like a vending machine's coin slot: it's shaped to accept coins, not bills, and it tells you up front — before you insert anything — exactly what will and won't fit. `duration_minutes: Literal[15, 30, 60]` is a slot shaped for exactly three coins; nothing else fits, no matter how politely you ask.

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

    Args:
        attendee_email: Valid email address of the attendee.
        duration_minutes: Meeting length. Must be 15, 30, or 60.
        topic: Short description of what the meeting is about.

    Returns:
        Confirmation string with booked time.
    """
    return f"Booked {duration_minutes} min with {attendee_email}: '{topic}'."
```
The `@function_tool` decorator turns these type hints and the docstring into a schema the model reads before it ever calls the function. If a user asks for a 45-minute meeting, the `Literal[15, 30, 60]` type makes that request impossible to pass through — the contract itself prevents the bad call, at zero runtime cost.

### The Pitfall
Treating the docstring as optional filler. Agent Factory calls it out directly: write it like you're describing the tool to a new colleague, and **include when *not* to call it** — omitting that is the most common bug source in tool-using agents, because the model calls the right function at the wrong moment.

### Guiding Question (Answer This From Memory Later)
Why is a function's type-annotated signature a contract, not just documentation?

---

## Summary

- **TDG Cycle**: write the failing test first, let the agent generate code to pass it, then verify — the test is the specification, and it must come from you.
- **Primitive Types**: `str`, `int`, `float`, `bool` are the four boxes every value fits into, labeled with `: type` so both you and the agent know what belongs inside.
- **Collections**: `list` (ordered, mutable), `dict` (labeled pairs, the backbone of agent data), `set` (unique items), `tuple` (ordered, fixed) — four containers that cover almost all real data.
- **Functions as Contracts**: a typed signature plus a clear docstring tells both humans and models exactly what's accepted, what's returned, and when *not* to call it.
- **Why Together**: TDG only works if your tests can precisely state what "correct" means — and precision requires the type vocabulary from this lesson's other three concepts. You can't write `assert total_with_tax(100.0, 0.15) == 115.0` with confidence until you know exactly what a `float` promises.

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: What are the three steps of the TDG cycle, in order, and who does each step?
**Question 2**: What's the difference between what `list[str]` and `dict[str, bool]` each promise about their contents?
**Question 3**: How does a function's type hint like `Literal[15, 30, 60]` act as a safeguard, not just documentation, when an AI agent is calling that function?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector), Programming in the AI Era — Phase 1–2: Your First TDG Cycle, Primitive Types & Expressions, Strings & Collections, Functions as Contracts (Chapters 46–49); Python in the AI Era crash course (Part 2 & 4, type vocabulary and TDG-loop examples); Build AI Agents crash course (function_tool contract examples).
