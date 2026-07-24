# Lesson 7: Concurrency, APIs, and Automated Verification

## Purpose

SmartNotes has persistence and structure — now it becomes a product. This lesson covers making it runnable from a terminal, making it fast when it waits on slow things, giving it a web API, and automating verification so every change is checked before it ships.

## Guiding Questions

1. How does a Python script become a real command-line tool with subcommands and proper exit codes?
2. What problem does `async`/`await` actually solve, and when does it help?
3. What turns a Python function into a web API endpoint FastAPI can serve?
4. What does a CI/CD pipeline automate, and why does a required check matter more than a local one?

---

## Unix-Style CLI Tools

### The Idea
A command-line tool takes arguments from the terminal (`sys.argv`), parses them with `argparse` into structured options and subcommands, and communicates success or failure through exit codes — not just printed text.

### In Simple Words
Right now, using SmartNotes means opening a Python REPL and importing it like a library — nobody wants to do that just to add a note. A CLI tool turns that into typing one line in a terminal: `smartnotes add "Groceries" "milk, eggs"`. `argparse` is what reads that line and figures out which piece is the command (`add`) and which pieces are its arguments.

### Real Example
```python
import argparse
import sys

def main() -> None:
    parser = argparse.ArgumentParser(prog="smartnotes")
    subparsers = parser.add_subparsers(dest="command", required=True)

    add_parser = subparsers.add_parser("add", help="Add a new note")
    add_parser.add_argument("title")
    add_parser.add_argument("body")

    args = parser.parse_args()

    if args.command == "add":
        print(f"Added note: {args.title}")
        sys.exit(0)          # exit code 0 — success

if __name__ == "__main__":
    main()
```
Running `smartnotes add "Groceries" "milk, eggs"` parses `add` as the subcommand and `title`/`body` as its arguments. `sys.exit(0)` signals success to whatever ran the program — a shell script or CI pipeline checking `$?` after this command sees `0` and knows it worked; any nonzero value signals failure.

### The Pitfall
Forgetting exit codes entirely and only printing error messages. A human reading the terminal sees the printed error, but an automated script calling your CLI tool only checks the exit code — a tool that always exits `0` even on failure will silently corrupt any pipeline built on top of it.

### Guiding Question (Answer This From Memory Later)
How does a Python script become a real command-line tool with subcommands and proper exit codes?

---

## Async Python: Doing Many Slow Things at Once

### The Idea
When code needs to wait on several slow operations (API calls, network requests), `async`/`await` lets the program fire off all of them and handle responses as they arrive, instead of waiting for each one to finish before starting the next.

### In Simple Words
Doing things one after another is standing in 20 different queues, one at a time, finishing each before joining the next. `async` is joining all 20 queues at once and just responding to whichever one reaches you first — the total wait is roughly the time of the *slowest* queue, not the sum of all 20.

### Real Example
```python
import asyncio

async def query_llm(prompt: str) -> str:
    response = await call_the_api(prompt)   # pause here, let other work proceed
    return response

async def main() -> None:
    prompts = ["Summarize note 1", "Summarize note 2", "Summarize note 3"]
    results = await asyncio.gather(*(query_llm(p) for p in prompts))
    print(results)

asyncio.run(main())
```
If each API call takes about 0.1 seconds, running them one after another takes roughly 0.3 seconds for three calls; running them concurrently with `asyncio.gather` finishes in about the time of the slowest single call — because the total is capped by the slowest call, not the sum of all of them.

### The Pitfall
Assuming `async` makes your *own* code run faster. It doesn't speed up computation — it only helps when the program is *waiting* on something external (network, disk, another service). Wrapping pure number-crunching in `async`/`await` with nothing to wait on gains nothing and just adds complexity.

### Guiding Question (Answer This From Memory Later)
What problem does `async`/`await` actually solve, and when does it help?

---

## FastAPI: Your First Web API

### The Idea
FastAPI turns a Python function into a network-reachable endpoint using a decorator (`@app.get`, `@app.post`), and uses Pydantic models to check incoming requests automatically, rejecting malformed ones before your function body even runs.

### In Simple Words
An endpoint is an address on the internet your program listens at — `POST /notes` is "send me a new note here." The route handler is just the ordinary Python function that runs when a request hits that address. Pydantic is the bouncer at the door: it checks the request's shape before your function ever sees it, so your code never has to defensively check "did they actually send a title?"

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
    done: bool

@app.post("/notes")
async def create_note(note: NoteIn) -> NoteOut:
    return NoteOut(id=1, title=note.title, body=note.body, done=False)
```
`@app.post("/notes")` marks `create_note` as the handler for `POST /notes`. The parameter `note: NoteIn` tells FastAPI to parse the incoming request body as JSON, validate it against `NoteIn`'s fields, and reject the request automatically if `title` or `body` is missing — your function body only ever runs on data that's already been checked.

### The Pitfall
Writing manual `if` checks inside the handler for things Pydantic already validates ("if not note.title: raise error"). If `NoteIn` requires `title: str`, a request missing it never reaches your function at all — FastAPI rejects it at the boundary. Trust the type system you already specified; don't re-verify what the framework guarantees.

### Guiding Question (Answer This From Memory Later)
What turns a Python function into a web API endpoint FastAPI can serve?

---

## CI/CD: Automating the Verification You Already Do By Hand

### The Idea
CI (Continuous Integration) automatically tests code on every change; CD (Continuous Delivery) automatically deploys code that passes. A required CI check with branch protection means work that fails verification literally cannot be merged — no matter what anyone (human or agent) claims about it.

### In Simple Words
Right now you run `ruff`, `pyright`, and `pytest` by hand before you trust a change. CI is hiring someone to do exactly that, automatically, every single time anyone pushes a change — and refusing to let the change through the door until it passes, the same rigor you already apply, just enforced without you having to remember to run it.

### Real Example
A local pre-commit hook is the first line of defense:
```bash
# .git/hooks/pre-commit — runs before every commit
#!/bin/sh
uv run ruff check --silent && uv run pytest --quiet || {
  echo "pre-commit: lint or tests failed — commit blocked"; exit 1;
}
```
But a local hook can be bypassed (`git commit --no-verify`), so it's the first fence, not the last. The last fence is the platform itself: a required GitHub Actions check plus branch protection on `main` means a pull request that fails CI **cannot merge**, full stop — the platform enforces what no promise or good intention can:
```yaml
# .github/workflows/ci.yml (conceptual shape)
on: [pull_request]
jobs:
  verify:
    steps:
      - run: uv run ruff check
      - run: uv run pyright
      - run: uv run pytest
```
With branch protection requiring this workflow to pass, a developer — or an AI agent — pushing broken code gets blocked automatically, before it ever reaches `main`.

### The Pitfall
Relying on the local pre-commit hook alone and assuming it's enough. Since `--no-verify` bypasses it in seconds, a local hook is a convenience, not a guarantee. The guarantee only exists once the check is required at the platform level, where it can't be skipped by anyone in a hurry.

### Guiding Question (Answer This From Memory Later)
What does a CI/CD pipeline automate, and why does a required check matter more than a local one?

---

## Summary

- **CLI Tools**: `argparse` turns terminal arguments into structured subcommands; exit codes (0 for success, nonzero for failure) are how automated callers — not just humans — know whether it worked.
- **Async Python**: `async`/`await` overlaps *waiting* time on slow external operations (APIs, network); it doesn't speed up pure computation.
- **FastAPI**: `@app.post(...)` plus a Pydantic model turns a function into a validated network endpoint — bad requests never reach your function body at all.
- **CI/CD**: a local pre-commit hook is a convenience that can be bypassed; a required CI check with branch protection is an actual guarantee nothing broken reaches `main`.
- **Why Together**: this is where SmartNotes becomes reachable three ways — terminal, async pipelines, and the network — while CI/CD makes sure every one of those surfaces stays verified automatically, not just when you remember to check by hand.

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: Why does an automated script care about a CLI tool's exit code even if the tool already printed an error message?
**Question 2**: If a function does heavy number-crunching with no network calls, does wrapping it in `async`/`await` help? Why or why not?
**Question 3**: What's the difference in guarantee between a local pre-commit hook and a required, branch-protected CI check?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector), Programming in the AI Era — Phase 7: CLI & Concurrency — Unix-Style CLI Tools, Async Python, FastAPI: Your First Web API (Chapters 65–67), Phase 8: Production Systems — CI/CD, Git Workflows & Observability (Chapter 68); Python in the AI Era crash course (Part 3, async/await examples); Deploying Agent Harness crash course (FastAPI concepts); Harness Engineering crash course (pre-commit hook and CI branch-protection examples); Agent Factory Glossary (CI/CD entry).
