# Lesson 23: Production CRUD — Errors, Dependency Injection, and Config

## Purpose

A single route is a demo. A real API needs the full CRUD cycle, explicit error responses instead of silent failures, shared setup that doesn't repeat across every route, and configuration that doesn't hardcode secrets. This lesson covers all four.

## Guiding Questions

1. What are the four CRUD operations, and how does each map onto an HTTP method?
2. What does "fail closed" mean, and why is it safer than a graceful-sounding guess?
3. What problem does FastAPI's dependency injection (`Depends`) actually solve?
4. Why must environment variables be loaded before any module that reads them?

---

## Full CRUD Operations

### The Idea
CRUD — Create, Read, Update, Delete — maps directly onto HTTP methods: `POST` creates, `GET` reads, `PUT`/`PATCH` updates, `DELETE` removes. A complete API exposes all four for each resource it manages, not just the read path.

### In Simple Words
Think of a filing cabinet. Create is adding a new folder. Read is pulling one out to look at it. Update is replacing what's inside without changing the label. Delete is removing the folder entirely. A real API needs to support all four actions on its data, not just letting you look.

### Real Example
Extending Lesson 22's `Note` API to the full cycle:
```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI()
notes_db: dict[int, "NoteOut"] = {}
next_id = 1

class NoteIn(BaseModel):
    title: str
    body: str

class NoteOut(BaseModel):
    id: int
    title: str
    body: str

@app.post("/notes")
def create_note(note: NoteIn) -> NoteOut:
    global next_id
    created = NoteOut(id=next_id, title=note.title, body=note.body)
    notes_db[next_id] = created
    next_id += 1
    return created

@app.get("/notes/{note_id}")
def read_note(note_id: int) -> NoteOut:
    if note_id not in notes_db:
        raise HTTPException(status_code=404, detail="Note not found")
    return notes_db[note_id]

@app.put("/notes/{note_id}")
def update_note(note_id: int, note: NoteIn) -> NoteOut:
    if note_id not in notes_db:
        raise HTTPException(status_code=404, detail="Note not found")
    updated = NoteOut(id=note_id, title=note.title, body=note.body)
    notes_db[note_id] = updated
    return updated

@app.delete("/notes/{note_id}")
def delete_note(note_id: int) -> dict[str, str]:
    if note_id not in notes_db:
        raise HTTPException(status_code=404, detail="Note not found")
    del notes_db[note_id]
    return {"status": "deleted"}
```
Each operation gets its own test, the same discipline as Lesson 22: create a note, then read it back; update it, then confirm the change; delete it, then confirm a second read returns 404.

### The Pitfall
Building only the Create and Read operations and treating Update/Delete as optional extras. A resource users can create but never correct or remove isn't a complete API — it's a one-way data sink that accumulates mistakes forever.

### Guiding Question (Answer This From Memory Later)
What are the four CRUD operations, and how does each map onto an HTTP method?

---

## Error Handling: Fail Closed, Not Gracefully Wrong

### The Idea
"Fail closed" means that when something goes wrong — a missing dependency, an unauthorized request, a broken connection — the system says so plainly rather than quietly improvising a plausible-looking but wrong response. In FastAPI, this means raising an explicit `HTTPException` with a clear status code instead of returning a guessed or default value.

### In Simple Words
An ATM that can't verify your balance locks and tells you so — it doesn't guess a number and hand you cash based on a guess. Agent Factory names this trap directly: if something in your system is missing or broken, and the code has enough general knowledge to *improvise* an answer anyway, that improvised answer is far more dangerous than an honest "I can't complete this right now" — because nobody can tell the difference until real damage is done.

### Real Example
```python
from fastapi import HTTPException

@app.get("/notes/{note_id}")
def read_note(note_id: int) -> NoteOut:
    if note_id not in notes_db:
        # Fail closed: say plainly it doesn't exist — never return a guessed default
        raise HTTPException(status_code=404, detail="Note not found")
    return notes_db[note_id]
```
The same discipline scales up: a `GET /health` endpoint that reports exactly which backends are actually active (`{"status": "ok", "postgres": false, "sandbox": false}`) rather than a bare `{"status": "ok"}` that hides which pieces are actually working — the health check itself should fail closed by being explicit about partial degradation, not silently claiming full health.

### The Pitfall
Catching an error and returning a default or empty value "to keep things running smoothly." A route that silently returns an empty list when its data source is actually unreachable looks like success to the caller — exactly the "quietly become a chatbot" failure mode Agent Factory warns about, just relocated to an API instead of a conversational agent.

### Guiding Question (Answer This From Memory Later)
What does "fail closed" mean, and why is it safer than a graceful-sounding guess?

---

## Dependency Injection: Shared Setup Without Repetition

### The Idea
FastAPI's `Depends` lets a route declare something it needs (a database connection, a config value, an authenticated user) as a parameter, and FastAPI resolves and supplies it automatically — without every route re-implementing the same setup logic.

### In Simple Words
Instead of every single route in your API separately figuring out "which database should I talk to, and is it even available," you write that logic once as a dependency, and every route that needs a database connection just asks for one — the same way a Context object (Lesson 17) gave a tool access to shared, session-scoped state without threading it through every argument by hand.

### Real Example
A dependency that picks the right backend based on what's actually configured — directly reflecting Agent Factory's own graceful-degradation pattern (falling back to SQLite when a full database URL isn't set):
```python
from fastapi import Depends
import os

def get_db_connection():
    database_url = os.environ.get("DATABASE_URL")
    if database_url:
        return connect_to_postgres(database_url)
    return connect_to_sqlite("local.db")   # graceful fallback, not a silent guess

@app.get("/notes/{note_id}")
def read_note(note_id: int, db=Depends(get_db_connection)) -> NoteOut:
    return db.fetch_note(note_id)
```
Every route that declares `db=Depends(get_db_connection)` gets the correctly-chosen connection automatically — the fallback logic lives in exactly one place, not copy-pasted into every route that needs a database.

### The Pitfall
Duplicating setup logic (config reading, connection selection) inside every route function instead of factoring it into a dependency. Beyond the repetition, this means a change to the fallback logic — say, adding a third backend option — requires editing every route individually instead of the one shared dependency function.

### Guiding Question (Answer This From Memory Later)
What problem does FastAPI's dependency injection (`Depends`) actually solve?

---

## Environment Variables: Config Outside Your Code

### The Idea
Environment variables keep sensitive or environment-specific settings (API keys, database URLs) outside your source code, typically loaded from a `.env` file. In Python, that loading must happen *before* any module that reads those variables at import time — otherwise you get a confusing error instead of a clear "missing config" message.

### In Simple Words
An environment variable is a setting your program reads at startup rather than one baked permanently into the code — the same file never has to change between your laptop and a production server, only the `.env` file's contents differ. But the *order* of loading matters: if a room's lights are wired to a switch that hasn't been flipped yet, walking in before flipping it just leaves you in the dark, confused about why nothing works, not receiving a helpful sign explaining the light switch is nearby but off.

### Real Example
The concrete footgun and its fix: `load_dotenv()` must run before any project module that reads environment variables. Because Python executes a module's top-level code the moment it's imported, a module that reads `os.environ["DATABASE_URL"]` at the top level will raise a `KeyError` the instant anything imports it — *unless* dotenv already loaded the `.env` file first:
```python
# main.py — correct order
from dotenv import load_dotenv
load_dotenv()                          # MUST run before the next import

from app.config import DATABASE_URL    # this module reads os.environ at import time
from app.routes import router
```
Get the order wrong, and the failure looks like a confusing `KeyError` buried deep in an import chain — not a clear "you forgot to set up your `.env` file" message.

### The Pitfall
Assuming any `KeyError` involving `os.environ` means the variable is genuinely missing from `.env`. It's just as often an ordering bug — the variable exists, but something tried to read it before `load_dotenv()` ran. Check the import order before assuming the `.env` file itself is incomplete.

### Guiding Question (Answer This From Memory Later)
Why must environment variables be loaded before any module that reads them?

---

## Summary

- **CRUD**: Create (`POST`), Read (`GET`), Update (`PUT`/`PATCH`), Delete (`DELETE`) — a complete API needs all four, not just the read path.
- **Fail Closed**: an explicit `HTTPException` with a clear status beats a silently-guessed default — a system should say "I can't do this" rather than improvise something plausible-but-wrong.
- **Dependency Injection**: `Depends` factors shared setup (like choosing a database backend) into one place every route can request, instead of duplicating that logic everywhere.
- **Environment Variables**: keep secrets and config outside your code in `.env`, but load them *before* any module reads them at import time, or you'll chase a confusing `KeyError` instead of a clear config error.
- **Why Together**: these four turn a single working route into a real, resilient service — complete operations, honest failures, shared setup, and safely-managed configuration.

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: Which HTTP method corresponds to each of the four CRUD operations?
**Question 2**: Why is silently returning an empty result on a broken data source worse than raising an explicit error?
**Question 3**: If two different routes both need to choose between a Postgres connection and a SQLite fallback, what FastAPI feature avoids writing that choice logic twice?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector), Connector-Native Apps crash course — Concept 11 (Fail Closed — Don't Quietly Become a Chatbot); Deploying Agent Harness crash course — Decision 1 (graceful degradation, health-check reporting); Build AI Agents crash course (the `load_dotenv()` ordering footgun); Agent Factory Glossary (Environment Variable / `.env`); Building Agent Factories — Chapter 70: FastAPI for Agents (Full CRUD Operations, Error Handling, Dependency Injection, Environment Variables).
