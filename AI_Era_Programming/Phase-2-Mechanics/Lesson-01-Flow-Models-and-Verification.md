# Lesson 3: Tests as Specification I — Flow, Models, and Verification

## Purpose

Your specifications are only as good as the logic and data they describe. This lesson gives you the three remaining pieces: how code makes decisions and repeats, how to replace fragile dicts with structured data pyright can check, and how pytest turns your assertions into an automated verification layer.

## Guiding Questions

1. How does Python decide between multiple outcomes, and what does indentation actually mean?
2. Why does a `dataclass` beat a raw dict for representing real data?
3. What makes pytest more than "a way to run asserts"?

---

## Control Flow: Decisions and Repetition

### The Idea
Code makes decisions with `if`/`elif`/`else` and repeats with `for` and `while`. Indentation isn't decoration — it's structure: it's how Python knows what belongs inside a decision or a loop.

### In Simple Words
Think of an `if`/`elif`/`else` chain as a bouncer checking a line of conditions top to bottom, and stopping at the very first one that's true — everything after that gets ignored, no matter how true it also is. A `for` loop is "do this once for each thing in the pile"; a `while` loop is "keep doing this as long as the condition holds."

### Real Example
```python
def grade_for(score: int) -> str:
    if score >= 90:
        return "A"
    elif score >= 70:
        return "B"
    elif score >= 50:
        return "C"
    else:
        return "F"
```
Predict `grade_for(72)` before running: it's `"B"` — `72 >= 90` is false, so Python falls to the next branch; `72 >= 70` is true, so it stops there and never checks the rest. Read the chain as *"check these in order; take the first match."*

Once you can read a `for` loop, you can read the single most common Python one-liner, the **comprehension** — a `for` loop folded onto one line:
```python
prices: list[int] = [10, 250, 50, 400]
expensive = [price for price in prices if price > 100]   # [250, 400]
```
Read it left to right: *"give me `price`, for each `price` in `prices`, if `price > 100`."*

### The Pitfall
Misreading `elif` chains as "check all of these" instead of "stop at the first true one." A generated function with overlapping conditions (`score >= 70` then later `score >= 50`) can look buggy until you remember only the *first* matching branch ever runs.

### Guiding Question (Answer This From Memory Later)
How does Python decide between multiple outcomes, and what does indentation actually mean?

---

## Dataclasses: Giving Data a Defined Shape

### The Idea
A raw dict (`{"title": "Meeting", "done": False}`) is flexible but dangerous — a typo'd key or a wrong type passes silently and corrupts everything downstream. A `dataclass` fixes this by giving your data a defined, typed shape that pyright can check before the code ever runs.

### In Simple Words
A dict is a sticky note where you can write any label you want, including a typo, and nobody stops you. A dataclass is a printed form with the fields already labeled — `title`, `done` — so if you try to fill in a field that doesn't exist on the form, you find out immediately instead of discovering it three steps later when something breaks mysteriously.

### Real Example
```python
from dataclasses import dataclass

@dataclass
class Note:
    title: str
    body: str
    done: bool = False     # default value
```
Notice there's no `__init__` method — the `@dataclass` decorator writes that setup method for you. You just list the attributes and their types, and you get `Note(title="Groceries", body="milk, eggs", done=False)` for free, with each field type-checked.

Compare the fragility this replaces:
```python
# The old way — a typo here fails silently at runtime, maybe hours later
note = {"titel": "Meeting", "done": False}   # "titel" — typo, nothing catches it
print(note["title"])   # KeyError, but only when this line finally runs
```
With a dataclass, `note.titel` instead of `note.title` is caught by pyright *before* you ever run the program — the same class of error the type-checking from Lesson 1 was built to catch.

### The Pitfall
Reaching for a dataclass when you actually need runtime validation, not just structure. A dataclass checks *shape* at development time (via pyright); it does **not** stop you from constructing `Note(title=123, body="x", done="not a bool")` at runtime with the wrong types. That stronger guarantee — actual runtime validation — is what Pydantic's `BaseModel` adds, which you'll meet directly in Lesson 4.

### Guiding Question (Answer This From Memory Later)
Why does a `dataclass` beat a raw dict for representing real data?

---

## pytest: Assertions as an Automated Verification Layer

### The Idea
pytest is Python's testing framework: you write test functions containing `assert` statements describing what code *should* do, and pytest runs them all automatically, reporting exactly which passed and which failed.

### In Simple Words
An `assert` is a one-line promise: "I am declaring that this must be true — fail loudly if it isn't." pytest's job is to go find every one of those promises in your project and check every single one of them for you, every time, instead of you eyeballing the output and hoping it looks right.

### Real Example
```python
def calculate_gst(amount: int) -> int:
    return round(amount * 0.18)

def test_gst_on_1000() -> None:
    assert calculate_gst(1000) == 180
```
This test says: "when I calculate GST on Rs. 1,000, the answer must be Rs. 180." If `calculate_gst` returns 170 instead, `pytest` reports the test failed — catching the bug before it ever reaches a customer.

Real projects need more than one hard-coded case sharing setup. A common pattern — building a small reusable object once and reusing it across multiple test functions — looks like this:
```python
class InMemoryStore:
    def __init__(self) -> None:
        self._totals: dict[str, int] = {}

    def add(self, key: str, amount: int) -> None:
        self._totals[key] = self._totals.get(key, 0) + amount

def test_single_addition() -> None:
    store = InMemoryStore()
    store.add("gst", 180)
    assert store._totals["gst"] == 180

def test_multiple_additions_accumulate() -> None:
    store = InMemoryStore()
    for _ in range(3):
        store.add("gst", 180)
    assert store._totals["gst"] == 540
```
Each test function gets its own fresh `InMemoryStore` — that's the shape a pytest fixture formalizes: shared, reusable setup that every test can request without repeating the setup code by hand.

### The Pitfall
Writing one giant test that checks five unrelated things at once. When it fails, you don't know which of the five broke. Agent Factory's own discipline: a growing `tests/` folder full of small, focused asserts is what lets you keep trusting a project as the agent keeps changing it — each test is an alarm for exactly one regression.

### Guiding Question (Answer This From Memory Later)
What makes pytest more than "a way to run asserts"?

---

## Summary

- **Control Flow**: `if`/`elif`/`else` checks conditions top-to-bottom and stops at the first true match; `for` repeats once per item; indentation is structure, not style.
- **Dataclasses**: `@dataclass` gives data a typed, defined shape — pyright catches a typo'd field before the code runs, unlike a raw dict where the same typo fails silently at runtime.
- **pytest**: an `assert` is a one-line promise about correct behavior; pytest finds and checks every promise in your project automatically, and small focused tests (often sharing setup via a fixture-like reusable object) tell you exactly what broke.
- **Verification Layer**: together, typed control flow and typed data give pytest something precise to check — vague code produces vague, unhelpful test failures.
- **Why Together**: this is the moment "reading code" (Lesson 1) and "specifying with types" (Lesson 2) become a real verification pipeline — decisions and data now have a shape, and pytest can hold that shape accountable.

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: In an `if`/`elif`/`else` chain, what happens once Python finds a true condition — does it keep checking the rest?
**Question 2**: What specific class of error does a `dataclass` catch that a raw dict does not, and which tool catches it?
**Question 3**: Why is one large test that checks five things worse than five small, focused tests — connect this to what a failing test is supposed to tell you?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector), Programming in the AI Era — Phase 3a: Control Flow, Data Models with Dataclasses, pytest Deep Dive (Chapters 50–52); Python in the AI Era crash course (Part 2–3, control flow and dataclass examples); Agent Factory Glossary (pytest, dataclass, pyright entries).
