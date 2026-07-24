# Lesson 5: The Python Object Model

## Purpose

A flat pile of functions isn't an architecture. This lesson gives you the four building blocks of object-oriented Python — classes, the inheritance-vs-composition choice, special methods, and decorators/properties — so you can design systems for AI to implement, not just individual functions.

## Guiding Questions

1. What's the difference between a class (blueprint) and an object (instance), and when do you need a full class instead of a dataclass?
2. How do you decide between inheritance ("is-a") and composition ("has-a") when designing a system?
3. What are dunder methods, and why do they let your own objects behave like Python's built-in ones?
4. What does `@property` actually change about how a value is accessed?

---

## Classes and Instances: Blueprint and Object

### The Idea
A class is a blueprint bundling related data and the actions that belong with it. An object (or instance) is a specific thing built from that blueprint — one `Note` class, many actual notes built from it, each with its own state.

### In Simple Words
A class is like a cookie cutter; objects are the actual cookies. Every cookie shares the same shape (defined once, in the cutter), but each one is a separate cookie you can decorate differently. `self` inside the blueprint just means "this particular cookie, not the cutter."

### Real Example
```python
class Note:
    def __init__(self, title: str, body: str) -> None:
        self.title = title        # an attribute — data this note carries
        self.body = body
        self.done = False

    def mark_done(self) -> None:  # a method — an action this note can perform
        self.done = True
```
- `__init__` is the setup method — it runs once, automatically, when you build a new note.
- `self` means "this particular object" — every method takes `self` first so it knows which object it's working on.
- `mark_done` is a method: a function that lives inside the class and acts on the object.

```python
n: Note = Note(title="Groceries", body="milk, eggs")
print(n.done)     # False
n.mark_done()
print(n.done)     # True
```
`object.attribute` reads data; `object.method()` asks the object to do something. That one pattern unlocks most real Python — a Pydantic model is a class, an agent is an object, a database connection is an object.

### The Pitfall
Reaching for a full class when a `@dataclass` (Lesson 3) would do. Use a full class once a type needs *behavior* beyond just holding data — methods like `mark_done()` that change state, not just fields that get read. If all you need is structure, a dataclass is simpler and does the job.

### Guiding Question (Answer This From Memory Later)
What's the difference between a class (blueprint) and an object (instance), and when do you need a full class instead of a dataclass?

---

## Inheritance vs. Composition: is-a vs. has-a

### The Idea
Inheritance ("is-a") makes one class a specialized version of another and shares its behavior automatically. Composition ("has-a") builds a class out of other objects it contains and delegates to them. Agent Factory's SmartNotes project uses both: a `TextNote`/`CodeNote`/`LinkNote` inheritance hierarchy for note *types*, and a `NoteCollection` that uses composition to manage many notes.

### In Simple Words
Inheritance is "a `CodeNote` **is a** `Note`, plus it also knows how to syntax-highlight." Composition is "a `NoteCollection` **has a** list of notes inside it" — the collection isn't itself a note, it just contains and manages them. The test: if it doesn't make sense to say "X is a Y," you want composition, not inheritance.

### Real Example
```python
class Note:
    def __init__(self, title: str, body: str) -> None:
        self.title = title
        self.body = body

class CodeNote(Note):                          # CodeNote IS-A Note
    def __init__(self, title: str, body: str, language: str) -> None:
        super().__init__(title, body)           # reuse Note's setup
        self.language = language

    def highlighted(self) -> str:
        return f"```{self.language}\n{self.body}\n```"

class NoteCollection:                            # NoteCollection HAS-A list of notes
    def __init__(self) -> None:
        self.notes: list[Note] = []              # composition — contains, doesn't inherit

    def add(self, note: Note) -> None:
        self.notes.append(note)
```
`CodeNote(Note)` means `CodeNote` inherits everything `Note` has, then adds `language` and `highlighted()`. `super().__init__(...)` calls the parent's setup so you don't duplicate it. `NoteCollection` is *not* a kind of note — it manages a list of them, which is composition.

### The Pitfall
Reaching for inheritance by default because it feels more "object-oriented." Agent Factory frames this as a composition-first principle: composition is more flexible and less brittle than deep inheritance chains. Ask "is X really a specialized Y?" before inheriting — if the honest answer is "not really, it just needs to use one," compose instead.

### Guiding Question (Answer This From Memory Later)
How do you decide between inheritance ("is-a") and composition ("has-a") when designing a system?

---

## Special Methods (Dunders): Making Objects Pythonic

### The Idea
Dunder methods (double-underscore, like `__init__` or `__call__`) are special methods that let your own objects behave like Python's built-in ones — printable with `print()`, comparable with `==`, iterable with `for`, sized with `len()`.

### In Simple Words
Dunders are how you teach your custom object to speak Python's native language. Without `__eq__`, comparing two notes with `==` checks whether they're the exact same object in memory. With `__eq__` defined, you can teach Python what "equal" should actually mean for a note — say, same title and body.

### Real Example
```python
class Pipeline:
    def __init__(self, factor: float) -> None:
        self.factor = factor

    def __call__(self, x: float) -> float:   # runs when you "call" the object like a function
        return x * self.factor

pipeline = Pipeline(2.5)
print(pipeline(10.0))    # 25.0 — the object is called like a function
```
`__call__` is exactly why, in libraries like PyTorch, you write `model(x)` instead of `model.forward(x)` — the framework defines `__call__` to do setup work before running.

The SmartNotes `NoteCollection` from the previous concept uses the same idea to become iterable and sized:
```python
class NoteCollection:
    def __init__(self) -> None:
        self.notes: list[Note] = []

    def __len__(self) -> int:                 # makes len(collection) work
        return len(self.notes)

    def __iter__(self):                       # makes "for note in collection" work
        return iter(self.notes)
```
Now `len(my_collection)` and `for note in my_collection:` both work naturally, because the object has been taught to act like a native Python container.

### The Pitfall
Implementing `__eq__` without understanding it changes hashability — by default, defining `__eq__` on a class removes its ability to be used in a `set` or as a `dict` key, because Python can no longer assume the object is safely hashable. If you need both custom equality and hashability, you have to define `__hash__` too.

### Guiding Question (Answer This From Memory Later)
What are dunder methods, and why do they let your own objects behave like Python's built-in ones?

---

## Decorators and Properties: @property and Beyond

### The Idea
`@property` lets you access a method like it's a plain attribute — no parentheses — which is how you expose a computed value (like a word count derived from a note's body) as if it were stored data.

### In Simple Words
Without `@property`, getting a computed value looks like calling a function: `note.word_count()`. With `@property`, it looks like reading a field: `note.word_count`. The caller can't tell — and doesn't need to — whether the value is stored or calculated fresh every time.

### Real Example
```python
class Note:
    def __init__(self, title: str, body: str) -> None:
        self.title = title
        self.body = body

    @property
    def word_count(self) -> int:
        return len(self.body.split())
```
```python
n = Note(title="Groceries", body="milk eggs bread")
print(n.word_count)     # 3 — read like an attribute, no parentheses
```
`word_count` is never stored — it's recalculated from `self.body` every time it's read, which means it can never go stale or drift out of sync with the note's actual content. That's the advantage a plain stored attribute doesn't give you.

### The Pitfall
Overusing `@property` for anything expensive to compute. Since it looks like a free attribute read, callers won't expect it to be slow — if `word_count` had to make a network call every time, that surprise cost would be hidden behind ordinary-looking attribute syntax. Reserve `@property` for cheap, derived values.

### Guiding Question (Answer This From Memory Later)
What does `@property` actually change about how a value is accessed?

---

## Summary

- **Classes & Instances**: a class is a blueprint, an object is a specific thing built from it; reach for a full class once you need behavior, not just structure.
- **Inheritance vs. Composition**: inheritance ("is-a") shares behavior through a hierarchy; composition ("has-a") builds a class out of contained objects — default to composition unless "is-a" is genuinely true.
- **Dunders**: special double-underscore methods (`__call__`, `__eq__`, `__iter__`, `__len__`) teach your objects to behave like Python's built-ins — but `__eq__` quietly removes default hashability.
- **Properties**: `@property` exposes a computed method as if it were a plain attribute, always fresh, never stale — but should stay cheap since it hides its cost behind ordinary syntax.
- **Why Together**: this is the shift from scripts to systems — SmartNotes goes from a pile of functions to `Note`, `CodeNote`, and `NoteCollection` objects with real behavior, comparability, iterability, and computed fields, all specified as class interfaces the agent implements against.

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: What's the practical test for choosing inheritance over composition, and what's the composition-first default?
**Question 2**: If you define `__eq__` on a class, what capability does the class lose by default, and why?
**Question 3**: How does the object model connect back to TDG (Lesson 2) — what does "specify" mean once you're designing classes instead of standalone functions?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector), Programming in the AI Era — Phase 5: The Python Object Model — Classes and Instances, Inheritance/Composition/Design, Special Methods, Decorators/Properties/Advanced Patterns (Chapters 58–61); Python in the AI Era crash course (Part 2–3, class/object and dunder-method examples).
