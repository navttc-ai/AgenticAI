# Lesson 6: Real-World Python — Files, Packages, and Tools

## Purpose

SmartNotes has classes with real behavior, but every time the program stops, the notes vanish. This lesson turns a library into an application: reading and writing real files, organizing code as a proper package, and using comprehensions and generators to transform data efficiently.

## Guiding Questions

1. How do you read and write text, JSON, and CSV files safely, and why does the format choice matter?
2. Why does a project need to become a *package*, and what does `__init__.py` actually do?
3. What's the difference between a comprehension and a generator, and when does that difference matter?

---

## Files & Data Processing: Making Data Persist

### The Idea
Reading and writing files is how a program's data survives after it stops running. Python's `pathlib` module gives you a clean way to work with file paths, and different formats (plain text, JSON, CSV) suit different needs — plain text for readability, JSON for structured objects, CSV for tabular data.

### In Simple Words
A running program's data lives in memory — like writing on a whiteboard that gets erased the moment you leave the room. Writing to a file is like copying that whiteboard onto paper before you go: it's still there when you come back. JSON and CSV are just different paper formats — JSON keeps the shape of an object, CSV keeps rows and columns.

### Real Example
Agent Factory's SmartNotes project uses exactly this to add persistence — a `FileManager` class that wraps text, JSON, and CSV so notes survive between sessions:
```python
import json
from pathlib import Path

def save_note_as_json(note: dict[str, str | bool], path: str) -> None:
    Path(path).write_text(json.dumps(note, indent=2))

def load_note_from_json(path: str) -> dict[str, str | bool]:
    return json.loads(Path(path).read_text())
```
```python
note = {"title": "Groceries", "body": "milk, eggs", "done": False}
save_note_as_json(note, "groceries.json")
restored = load_note_from_json("groceries.json")
print(restored["title"])   # "Groceries" — survived being written and read back
```
`json.dumps` turns a Python object into JSON text; `json.loads` turns it back. `Path(path).write_text(...)` and `.read_text()` are `pathlib`'s clean interface for file I/O — no manual `open()`/`close()` bookkeeping needed for simple text operations.

### The Pitfall
Choosing the wrong format for the job. JSON preserves structure (nested objects, booleans, typed fields) but isn't spreadsheet-friendly. CSV is spreadsheet-friendly but flattens everything to rows of text, losing type information (a `bool` becomes the literal string `"False"`, not a real boolean, unless you convert it back explicitly on read).

### Guiding Question (Answer This From Memory Later)
How do you read and write text, JSON, and CSV files safely, and why does the format choice matter?

---

## Modules & Packages: Organizing Code as a Real Project

### The Idea
A module is just a Python file; a package is a folder of modules with an `__init__.py` file marking it as importable. As a project grows past a handful of functions in one file, splitting it into a proper package — separate files for models, storage, search — keeps it navigable and lets other code import pieces of it cleanly.

### In Simple Words
One giant file with everything in it is like a junk drawer — technically everything's in there, but finding anything means digging through all of it. A package is that drawer split into labeled compartments: `models.py` for what a Note *is*, `storage.py` for how notes get saved, `search.py` for how you find them — each file focused on one job.

### Real Example
Agent Factory's SmartNotes goes from flat scripts to a proper package:
```text
smartnotes/
├── __init__.py       # marks this folder as an importable package
├── models.py         # the Note and NoteCollection classes
├── storage.py         # save/load functions (from Concept 1 above)
├── search.py           # search and filtering logic
└── __main__.py         # lets you run `python -m smartnotes`
```
```python
# models.py
class Note:
    def __init__(self, title: str, body: str) -> None:
        self.title = title
        self.body = body

# storage.py
from smartnotes.models import Note   # importing across files in the same package

def save(note: Note, path: str) -> None:
    ...
```
`__init__.py` can be empty — its mere presence is what tells Python "this folder is a package, not just a folder." The import `from smartnotes.models import Note` reaches into another file in the same package by its path, the same dot notation you've been reading since Lesson 5's `object.attribute`.

### The Pitfall
Circular imports — `models.py` importing from `storage.py` while `storage.py` also imports from `models.py`. Python can't resolve which one loads first, and the program crashes with an import error. The fix is usually a structural one: keep the fundamental data shapes (like `Note`) in a file that *nothing else in the package depends on*, so imports only ever flow one direction.

### Guiding Question (Answer This From Memory Later)
Why does a project need to become a *package*, and what does `__init__.py` actually do?

---

## Comprehensions, Generators & Functional Patterns

### The Idea
A comprehension builds a complete new list (or dict, or set) in one line by folding a `for` loop into an expression. A generator, marked with `yield` instead of `return`, hands back results one at a time on demand — never holding the whole result in memory at once.

### In Simple Words
A comprehension is baking an entire tray of cookies before handing any out — fast, but you need room to hold the whole tray. A generator is a cookie-baking machine that hands you one cookie at a time, only baking the next one when you ask — slower to get all of them, but you never need space for more than one at once.

### Real Example
```python
prices: list[int] = [10, 250, 50, 400]
expensive = [price for price in prices if price > 100]   # a comprehension — builds the full list now
```
That's the shape you already know from Lesson 3: `[expression for item in iterable if condition]`, read left to right as "give me `price`, for each `price` in `prices`, if `price > 100`."

Now the generator version, for when the data is too large to hold entirely in memory:
```python
from collections.abc import Iterator

def stream_notes(lines: list[str]) -> Iterator[str]:
    for line in lines:
        yield line.strip().lower()     # hand back one item, then pause
```
The return type `Iterator[str]` — not `list[str]` — is the tell-tale sign: this promises a *stream* you pull from one item at a time, not a finished pile. Streaming a million-record dataset through a generator instead of a list comprehension can dramatically cut peak memory, because the whole thing is never held at once.

Agent Factory's SmartNotes uses this exact pattern in its `NoteAnalytics` class — tag frequency and word-count statistics computed with comprehensions over the note collection, without ever loading every note's full text into a second copy in memory.

### The Pitfall
Defaulting to a comprehension out of habit even when the dataset is huge. If you only need to process items one at a time and never need the whole collection at once, a comprehension silently pays a memory cost a generator wouldn't. The tell: when you see `yield` in generated code, read it as "this produces a stream, not a pile" — and ask whether your own code should too.

### Guiding Question (Answer This From Memory Later)
What's the difference between a comprehension and a generator, and when does that difference matter?

---

## Summary

- **Files & Data Processing**: `pathlib` plus `json`/`csv` give SmartNotes persistence — pick the format based on whether you need structure (JSON) or spreadsheet-friendliness (CSV).
- **Modules & Packages**: a package is a folder of focused files marked importable by `__init__.py`; split by responsibility (models, storage, search) and keep imports flowing one direction to avoid circular imports.
- **Comprehensions vs. Generators**: comprehensions build the whole result now; generators (`yield`, `Iterator[...]`) stream results one at a time and can dramatically cut memory use on large data.
- **Why Together**: this is where SmartNotes stops being a library only developers can `import` and becomes a real application — data survives restarts, code is organized enough to navigate, and large-data operations don't waste memory.

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: Why would you choose JSON over CSV for saving a `Note` object, and what does CSV lose in the process?
**Question 2**: What's the one-sentence purpose of `__init__.py`, and what problem does a circular import cause?
**Question 3**: You need to process a million-row dataset but only ever look at one row at a time. Should you write a comprehension or a generator, and why?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector), Programming in the AI Era — Phase 6: Real-World Python — Files and Data Processing, Modules and Packages, Comprehensions/Generators/Functional Patterns (Chapters 62–64); Python in the AI Era crash course (Part 2–3, comprehension and generator/yield examples).
