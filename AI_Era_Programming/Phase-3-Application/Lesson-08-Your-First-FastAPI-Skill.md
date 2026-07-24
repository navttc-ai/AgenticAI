# Lesson 22: Your First FastAPI Skill — Routes, Testing, and Pydantic Models

## Purpose

This chapter flips the usual order: you build a FastAPI skill *before* you're taught FastAPI, then spend the rest of the chapter understanding and hardening what you built. This lesson covers that skill-first pattern, your first route, testing it, and validating requests with Pydantic.

## Guiding Questions

1. Why does this chapter have you build a FastAPI skill *before* teaching FastAPI itself?
2. What are the three pieces every FastAPI route needs, at minimum?
3. What does `TestClient` let you verify without a real running server?
4. What happens to a malformed request before your route handler ever runs?

---

## Build Your FastAPI Skill: The Skill-First Pattern

### The Idea
Rather than "learn FastAPI, then maybe build a skill," this chapter's Lesson 0 has you build a `fastapi-agent-api` skill first — using the skill-creation tools from earlier in the book — and then spends the rest of the chapter deepening and hardening what you already own, rather than teaching from a blank page.

### In Simple Words
The usual way to learn a tool is: study it, then eventually build something. The skill-first way flips that: build something real immediately (even imperfectly), and then every lesson afterward makes that real thing better. You're not learning FastAPI in the abstract — you're improving a skill you already own and can point to.

### Real Example
The shift in framing this represents: the traditional mindset is "teach me FastAPI." The skill-first mindset is "I own a FastAPI skill — help me make it better." By the end of this chapter, your skills folder holds something concrete:
```
.claude/skills/
├── skill-creator/           # from earlier in the book
├── fetching-library-docs/   # from earlier in the book
└── fastapi-agent-api/       # NEW — built in this chapter's Lesson 0
```
This skill joins a growing "Digital FTE toolkit" — the same accumulating-capability idea from Lesson 20's skill composition, now applied to your own personal collection rather than one agent's.

### The Pitfall
Treating Lesson 0's first draft as something to get "right" before moving on. The whole design of this chapter is iterative — you're not expected to build a finished, hardened API in the first pass. You're building something real enough to improve, which is a different (and lower) bar than building something perfect.

### Guiding Question (Answer This From Memory Later)
Why does this chapter have you build a FastAPI skill *before* teaching FastAPI itself?

---

## Hello FastAPI: Your First Route

### The Idea
A FastAPI app is a Python file that creates an `app` object and decorates functions as endpoints. An endpoint is a specific path your API handles (like `GET /health`); a route handler is the plain Python function that runs when that endpoint is called.

### In Simple Words
Think of `app` as the whole building, and each `@app.get(...)`-decorated function as a labeled door into it. When a request arrives at a specific address, FastAPI routes it straight to the function behind the matching door — you don't write any of the plumbing that figures out which door to open.

### Real Example
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/health")
def health_check() -> dict[str, str]:
    return {"status": "ok"}

@app.get("/greet/{name}")
def greet(name: str) -> dict[str, str]:
    return {"message": f"Hello, {name}!"}
```
`Uvicorn` is the program that actually runs this file and connects the network to your handlers — you'd start it with something like `uvicorn main:app`. The mental model stays this simple even as the app grows: each function receives checked data, does its work, and returns data FastAPI turns into JSON.

### The Pitfall
Forgetting that route paths are matched in the order they're defined, and a more general pattern can shadow a more specific one if it's declared first. `/greet/{name}` before a more specific `/greet/admin` route would catch requests meant for the specific route — order matters when paths could overlap.

### Guiding Question (Answer This From Memory Later)
What are the three pieces every FastAPI route needs, at minimum?

---

## Pytest Fundamentals: Testing an API Without a Running Server

### The Idea
FastAPI's `TestClient` lets you send requests directly to your app in a test — no real server needs to be running, no real network call happens — and assert on the response exactly the way you'd assert on any function's return value from Lesson 3's pytest fundamentals.

### In Simple Words
Instead of starting the whole restaurant to test whether the kitchen can make one dish correctly, `TestClient` lets you hand the order straight to the kitchen and check the plate that comes back — same test of correctness, without needing the doors open and customers walking in.

### Real Example
```python
from fastapi.testclient import TestClient
from main import app

client = TestClient(app)

def test_health_check() -> None:
    response = client.get("/health")
    assert response.status_code == 200
    assert response.json() == {"status": "ok"}

def test_greet() -> None:
    response = client.get("/greet/Ayesha")
    assert response.status_code == 200
    assert response.json()["message"] == "Hello, Ayesha!"
```
This is the exact same TDG discipline from Lesson 2, applied to routes instead of plain functions: write the assertion that defines "correct" for this endpoint, run it, and know immediately if the route breaks — no manual clicking through a browser or curl command required.

### The Pitfall
Only testing that a route returns *some* successful response, without checking the actual content. `assert response.status_code == 200` alone doesn't catch a route that returns the wrong data with a correct status code — pair the status check with an assertion on the actual response body, the same "test the content, not just that something happened" discipline from Lesson 3.

### Guiding Question (Answer This From Memory Later)
What does `TestClient` let you verify without a real running server?

---

## POST and Pydantic Models: Validating Requests at the Boundary

### The Idea
A Pydantic model describes the exact shape of expected request data. When a route parameter is typed as a Pydantic model, FastAPI parses the incoming JSON body, validates it against that shape, and rejects malformed requests automatically — your route function only ever runs on data that's already passed validation.

### In Simple Words
This is Lesson 7's FastAPI validation, now the centerpiece instead of a preview: the Pydantic model is a bouncer checking IDs at the door. By the time a request reaches your actual route function, it's already been confirmed to have the right shape — your code never has to defensively ask "wait, did they actually send a title?"

### Real Example
```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

class NoteIn(BaseModel):
    title: str
    body: str

class NoteOut(BaseModel):
    id: int
    title: str
    body: str

@app.post("/notes")
def create_note(note: NoteIn) -> NoteOut:
    return NoteOut(id=1, title=note.title, body=note.body)
```
Testing both the success case and the validation failure — pinning the behavior the way Lesson 4 pinned Pydantic's rejection behavior with `pytest.raises`:
```python
def test_create_note_success() -> None:
    response = client.post("/notes", json={"title": "Groceries", "body": "milk, eggs"})
    assert response.status_code == 200
    assert response.json()["title"] == "Groceries"

def test_create_note_missing_field() -> None:
    response = client.post("/notes", json={"title": "Groceries"})  # missing "body"
    assert response.status_code == 422   # FastAPI's validation-error status code
```
A request missing `body` never reaches `create_note`'s function body at all — FastAPI returns a `422 Unprocessable Entity` automatically, before your code runs.

### The Pitfall
Writing manual `if` checks inside the route for fields the Pydantic model already requires. If `NoteIn` requires `body: str`, a request without it is rejected by FastAPI before your function starts — adding a redundant `if not note.body: raise ...` inside the handler is dead code that can never actually trigger.

### Guiding Question (Answer This From Memory Later)
What happens to a malformed request before your route handler ever runs?

---

## Summary

- **Skill-First Pattern**: build a real (if imperfect) FastAPI skill immediately, then spend the chapter improving what you own rather than learning from a blank page.
- **Your First Route**: `app` is the whole API; each decorated function is a labeled door; route order matters when paths could overlap.
- **Pytest + TestClient**: test routes exactly like Lesson 3's plain functions — send a request, assert on the response, no real server needed.
- **Pydantic Validation**: a typed request parameter is a bouncer at the door — malformed requests never reach your function body, returning `422` automatically.
- **Why Together**: these four give you a complete, tested, validated first API — the foundation every later FastAPI lesson (CRUD, auth, streaming) builds directly on top of.

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: What's the mindset shift this chapter's skill-first pattern is training — "teach me FastAPI" versus what?
**Question 2**: Why is `assert response.status_code == 200` alone not a complete test for a route?
**Question 3**: If a `NoteIn` Pydantic model requires both `title` and `body`, and a POST request is missing `body`, what HTTP status code does the client receive, and does the route function ever execute?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector), Deploying Agent Harness crash course — Concept 4 and Stack Primer (FastAPI fundamentals, endpoints, route handlers, Pydantic validation); Python in the AI Era crash course (Pydantic pin-it-with-a-test pattern); Building Agent Factories — Chapter 70: FastAPI for Agents (Build Your FastAPI Skill, Hello FastAPI, Pytest Fundamentals, POST and Pydantic Models).
