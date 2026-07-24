# Lesson 8: Securing and Shipping — Capstone

## Purpose

This is the last Python lesson. It closes the loop: auditing AI-generated code for what it quietly gets wrong on security, then proving the entire discipline transfers by finishing SmartNotes and building a second project — QuizForge — with no scaffolding at all.

## Guiding Questions

1. What kinds of mistakes does AI-generated code make on security specifically, and why can't tests alone catch them?
2. What does it mean to be the "human firewall" for a project an agent mostly wrote?
3. What does the QuizForge capstone actually prove that finishing SmartNotes alone wouldn't?

---

## Security Review: You Are the Human Firewall

### The Idea
AI optimizes for functionality, not security — it confidently writes code that runs correctly on the happy path while quietly introducing vulnerabilities. Security-sensitive code (passwords, API keys, payments, user data) is non-negotiable: it must be read carefully, and ideally reviewed by someone who understands the specific risk, not just tested and trusted.

### In Simple Words
A test proves your code does what your test says — no more. A test doesn't know to ask "could someone else's request accidentally read this user's data?" or "is this secret about to get committed to a public repository?" Those are security questions, and passing tests are silent about them entirely. That's why security review is a separate, deliberate step, not something automated tests replace.

### Real Example
Two real security bugs AI commonly introduces, both drawn directly from Agent Factory's own worked examples:

**1. Hardcoded secrets.** An API key belongs in the environment, never in a file that gets committed:
```python
# WRONG — the agent might write this by default
API_KEY = "sk-proj-abc123xyz..."   # now it's in your git history forever

# RIGHT — read from the environment, nothing sensitive in the file
import os
API_KEY = os.environ["OPENAI_API_KEY"]
```
The explicit instruction Agent Factory gives: *"read the API key from an environment variable, never write it into a file we commit"* — then verify the diff yourself before approving it.

**2. Trusting the wrong source for identity.** If a function ever takes a `user_id` argument that could be supplied by an AI-driven request, and that value determines whose data gets read or written, you have a trust bug: a confused or manipulated request could name the wrong user. Identity must come from a verified, signed source (like an authentication token) — never from an argument the model or a user's message could influence directly.

### The Pitfall
Assuming a green test suite means the code is safe. Green tests prove the code does what *your tests* say — nothing more. A test suite with zero security-specific tests gives zero security signal, no matter how many other tests pass.

### Guiding Question (Answer This From Memory Later)
What kinds of mistakes does AI-generated code make on security specifically, and why can't tests alone catch them?

---

## Finishing SmartNotes: The Full Cycle, End to End

### The Idea
The SmartNotes capstone isn't a new concept — it's proof that every discipline from this course (reading, specifying with types, testing, debugging, object design, files and packages, concurrency, CI/CD, and now security) works together on one real, growing project, not just in isolated exercises.

### In Simple Words
Every earlier lesson taught you one tool in isolation — a hammer here, a level there. The capstone is building the actual house: the same tools, but now they all have to work together at once, on a project that's been accumulating decisions since Lesson 1.

### Real Example
By the end of Part 4, SmartNotes has grown into a complete, portfolio-grade application with a specific, checkable set of deliverables — not vague completion, but concrete artifacts:
- SDD specification documents (what each feature was supposed to do, written before it was built)
- Type definitions and an object model diagram (the `Note`/`NoteCollection` design from Lesson 5)
- A passing test suite covering the whole application, including security-specific tests from this lesson
- A green CI pipeline (Lesson 7) that verifies every future change automatically
- A security audit specifically covering what AI tends to miss

Each of these is something you can point to and say "I built this, and I can prove it's correct" — not "I asked AI for an app and it seemed to work."

### The Pitfall
Treating the capstone as "finish the remaining features" instead of "verify the whole system holds together." A project that passes each feature's individual tests can still fail as a whole — the CI pipeline running end-to-end, the security audit covering the *entire* app rather than the last feature added, is what actually closes the loop.

### Guiding Question (Answer This From Memory Later)
What does it mean to be the "human firewall" for a project an agent mostly wrote?

---

## QuizForge: Proving the Method Transfers

### The Idea
QuizForge is a second, independent project built from scratch with no scaffolding and no step-by-step walkthrough — the test of whether you actually learned the *method* (Test-Driven Generation, applied through reading, specifying, testing, debugging, and reviewing) rather than just memorizing SmartNotes' specific solutions.

### In Simple Words
If SmartNotes is the guided practice exam with the answer key nearby, QuizForge is the real exam, closed-book. Anyone can follow a walkthrough successfully. QuizForge asks a harder question: can you drive the same cycle — specify, test, generate, verify, debug — on a problem you've never seen laid out for you before?

### Real Example
The entire Part 4 arc concludes on this transfer: you finish with two portfolio-grade projects, SmartNotes (guided) and QuizForge (independent), and the explicit claim is that they *prove* you can drive the complete TDG cycle at production scale — not "understand it," but drive it, unsupervised, on new material.

Concretely, that means for QuizForge you do — without anyone naming the steps for you — everything this course built up to:
1. Write the specification and types before any code exists
2. Write the failing tests that define "correct"
3. Direct the agent to generate an implementation
4. Verify with `ruff`, `pyright`, and `pytest`
5. Debug systematically when something fails
6. Review for security issues before calling it done

### The Pitfall
Trying to make QuizForge "easier" by unconsciously reusing SmartNotes' exact structure instead of specifying QuizForge on its own terms. The point of an independent capstone is applying the *method* to a genuinely different problem — copying the previous project's shape defeats the purpose of testing transfer.

### Guiding Question (Answer This From Memory Later)
What does the QuizForge capstone actually prove that finishing SmartNotes alone wouldn't?

---

## Summary

- **Security Review**: AI writes confidently insecure code by default — hardcoded secrets and identity trusted from the wrong source are two concrete, common failures; tests don't catch either automatically.
- **Finishing SmartNotes**: the capstone proves every discipline from the course works together on one real system — specs, types, tests, debugging, object design, packaging, CI, and security, not just each in isolation.
- **QuizForge**: an independent, unscaffolded second project that tests whether the *method* transferred, not whether you can follow a walkthrough a second time.
- **Why Together**: this lesson is the proof-of-work for the entire Python track — you don't just know what TDG is, you can run the full cycle, securely, on a project nobody guided you through.

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: Name two specific security mistakes AI-generated code commonly makes, and explain why a passing test suite wouldn't catch either.
**Question 2**: What's the difference between "SmartNotes is feature-complete" and "SmartNotes' capstone deliverables are complete" — what does the second one add?
**Question 3**: Why does QuizForge need to be built without scaffolding for it to actually prove anything?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector), Programming in the AI Era — Phase 8: Production Systems — Security Review for AI-Generated Code (Chapter 69), Phase 9: Capstone — SmartNotes and QuizForge (Chapters 70–71); Programming in the AI Era overview page (deliverables and outcomes); Python in the AI Era crash course (Part 5, security and judgment section); Postgres for AI and Connector-Native Apps crash courses (API key and identity-trust examples).
