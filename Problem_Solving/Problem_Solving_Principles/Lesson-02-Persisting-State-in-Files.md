# Lesson 2: Problem-Solving Principles — Crash Course (Part 2)

## Purpose

Lesson 1 got AI to act, format, verify, and checkpoint its work. These 3 concepts close the loop: how AI remembers across sessions, how you keep it inside safe boundaries, and how you actually know what it did. Master these and you're not just prompting well — you're running the discipline a Gulf-region employer expects from an FDE.

---

## Guiding Questions

1. Why does AI forget everything the moment you close the conversation?
2. Why does giving AI *fewer* permissions actually let you trust it with *more* freedom?
3. Why can't you fully trust an AI output until you've watched how it was produced?

---

## The 3 Concepts

### Persisting State in Files: Make memory durable, not conversational

#### The Idea
From Agent Factory: when you close a session, AI forgets everything — decisions, rules, terminology, plans. The conversation is volatile; the filesystem is durable. The fix is a short **rules file** (`CLAUDE.md` or `AGENTS.md`) that AI reads automatically at the start of every new session, so you never re-explain the same conventions twice.

#### In Simple Words
Think of onboarding a new team member every single morning, from scratch, because they forget everything overnight. Exhausting, right? A rules file is like a one-page onboarding doc pinned to the project — the new "hire" reads it automatically before doing anything else. You write it once; it works forever.

#### Real Example
You're managing multiple curriculum projects for different clients — a ShopBot curriculum, a lab-management curriculum, an FDE career curriculum. Each has its own conventions: citation format, target word counts, tone. Instead of re-explaining "keep lessons 1300–1500 words, cite Agent Factory naturally" in every new chat, you write a 200-word `CLAUDE.md` per project folder. Every session inherits the rules instantly — no re-briefing, no drift between sessions.

#### The Pitfall
People either skip the rules file entirely (and re-explain everything every session) or bloat it into a 2,000-word encyclopedia that AI has to read on every message, slowing things down. The correction: keep it under 250 words at first, under 60 lines after a few weeks — a table of contents, not documentation.

#### Guiding Question (Answer This From Memory Later)
Why does AI forget everything the moment you close the conversation?

---

### Constraints and Safety: Limits are what let you walk away

#### The Idea
From Agent Factory: setting limits on what AI can touch doesn't slow it down — it's what lets you trust it enough to stop watching every action. The three universal levers are **scope** (what files/folders it can see), **connections** (what services it can reach), and **approvals** (when it pauses for your OK). Autonomy is climbed deliberately, one rung at a time, never granted by default.

#### In Simple Words
It's the difference between giving a new employee the master key to the whole building versus a badge that only opens their department. The badge isn't a punishment — it's what lets the manager stop personally escorting them everywhere. Fewer permissions, more trust, more freedom to walk away.

#### Real Example
You're building a curriculum-generation workflow that pulls from the Agent Factory SoR and writes lesson files. Rather than granting the agent full read/write access to your entire drive "to be safe," you scope it to only the project's `Lessons/` folder, set the SoR connector to read-only, and require approval before any file write outside that folder. When a stray prompt-injected instruction in a source document tries to get the agent to touch something else, the permission boundary — not your vigilance — blocks it.

#### The Pitfall
The common mistake is writing safety rules into the prompt ("don't touch the finance folder") instead of the tool's settings — prompt-level rules get forgotten by message 20. The correction: move any rule you keep repeating into the actual settings file, where it applies every session, permanently.

#### Guiding Question (Answer This From Memory Later)
Why does giving AI *fewer* permissions actually let you trust it with *more* freedom?

---

### Observability: You can only direct what you can see

#### The Idea
From Agent Factory: every meaningful action the agent takes should be visible to you in close to real time. Observability is how you debug a session that's drifted, how you build the track record needed to climb the autonomy ladder, and how you earn the right to trust an artifact — you trust the output only after the trace that produced it has earned it.

#### In Simple Words
Imagine grading a student's exam by only looking at the final answer, never their working. You'd miss the lucky guess, the copied answer, the shortcut that happened to work this time but won't next time. Watching the agent's step-by-step trace — at least once on any new task — is how you catch the version of "lucky guess" that AI produces.

#### Real Example
You're running a weekly "scan the SoR for new content and flag curriculum gaps" task at the walk-away autonomy level. One week, out of habit, you actually watch the execution trace instead of just checking the output file. You notice the agent opened a folder it had no reason to touch — a stale scope left over from an earlier task. You catch and fix the permission drift before it becomes a real incident, exactly the kind of habit that separates "I prompt AI" from "I run a disciplined AI workflow" on a portfolio.

#### The Pitfall
The common failure is watching the trace once when a task is new, then never watching again — even after the prompt, folder, or connector changes and quietly turns a "familiar" task into a new one. The correction: watch-once on every genuinely novel version of a task, not just the first time you ever ran that category of task.

#### Guiding Question (Answer This From Memory Later)
Why can't you fully trust an AI output until you've watched how it was produced?

---

## Summary

- **Persisting State in Files**: Write a short rules file so AI never has to be re-briefed on the same conventions every session.
- **Constraints and Safety**: Scope, connections, and approvals are the three levers — tighten them and you earn the right to walk away.
- **Observability**: Watch the trace, not just the artifact — trust is earned by what you saw happen, not by how polished the result looks.
- **Why Together**: These three are what turn a one-off good prompt into a repeatable, safe, and inspectable workflow — persistence removes re-explaining, constraints remove risk, observability removes blind trust.
- **Full Loop**: Combined with Lesson 1's Action, Structure, Verification, and Decomposition, all 7 principles form one operating discipline — the exact process an FDE can demonstrate, not just describe, in a portfolio or interview.

---

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: What problem does a rules file (`CLAUDE.md` / `AGENTS.md`) solve, and what are the three things it should contain?

**Question 2**: What are the three universal trust levers for constraining an AI agent?

**Question 3**: How does Observability let you safely climb the autonomy ladder that Constraints and Safety sets up?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector) — *Problem-Solving Crash Course*, Principles 5–7
