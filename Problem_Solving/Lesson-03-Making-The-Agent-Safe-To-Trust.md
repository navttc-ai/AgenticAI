# Lesson 3: Making the Agent Safe to Trust

## Purpose

Getting one clean result from an agent is Lesson 2. Trusting it enough to walk away — and knowing exactly what it did while you were gone — is a different skill entirely. These 4 concepts turn a tool you have to babysit into one you can genuinely delegate to.

---

## Guiding Questions

1. Why does an agent forget everything the moment you close the conversation, and what actually fixes that?
2. Why do limits on what an agent can touch make it *more* trustworthy, not less useful?
3. Why can you only direct what you can actually see the agent doing?
4. How do all seven principles from this track collapse into one repeatable four-step loop?

---

## The 4 Concepts

### Persisting State in Files

#### The Idea
When you close a conversation, the agent forgets everything — decisions, rules, terminology, all of it. The fix is a rules file (`CLAUDE.md` or `AGENTS.md`) that the agent reads automatically at the start of every session. Anything worth remembering across sessions belongs in a file, not left in a chat.

#### In Simple Words
It's like onboarding a new temp worker every single morning versus having one experienced employee who already knows the rules. A rules file is the difference — write the rules once, and every future session starts already knowing them.

#### Real Example
From Agent Factory: in a hiring-screen test, an agent without a rules file recommended ADVANCE for a candidate named Carlos based on his MBA and job titles. After a short rules file was added — including a credential-verification rule — the same agent, on the same resumes, caught that Carlos's MBA date predated the school's founding, and moved him to HOLD. Nobody re-typed the rule in the second run; it lived in the file. For ShopBot, a `CLAUDE.md` noting "never quote a price without checking the current catalog" catches exactly this kind of drift automatically, every week.

#### The Pitfall
The most common mistake is making the rules file too long — dumping the entire project history into it. The agent reads this file on *every* message, so a bloated file slows things down. Keep it under 250 words at first; treat it as a table of contents, not an encyclopedia.

#### Guiding Question (Answer This From Memory Later)
Why does an agent forget everything the moment you close the conversation, and what actually fixes that?

---

### Constraints and Safety: The Autonomy Ladder

#### The Idea
Limits aren't a problem — they're what makes it safe to give an agent more freedom. Three levers control this: scope (what files it can see), connections (what services it can reach), and approvals (when it pauses for your OK). Climb an "autonomy ladder" deliberately — from watching every action, to walking away, to letting it run unsupervised — and step back down whenever the task type changes.

#### In Simple Words
It's like giving a new driver a learner's permit before handing over the keys unsupervised. You don't start at "take the car wherever" — you start in the passenger seat, watching every turn, and earn more freedom as trust builds. The freedom is earned, not assumed.

#### Real Example
From Agent Factory: a hospital administrator gives an agent access to both patient records and general reports "so it can compare data" — now private patient information is sitting in the conversation unnecessarily. With proper scope limits, the agent only sees the general reports folder; patient data never enters the conversation at all. For your Timetable project, this means the scheduling agent gets read/write access to the schedule files, but read-only access to student records — it can reference them, never edit them.

#### The Pitfall
The failure that ends up in the news is always the same shape: an agent touched files nobody authorized, because permissions typed into a *prompt* get forgotten by message 20. Rules set in the tool's actual settings apply every session, with no exceptions — that's the fix.

#### Guiding Question (Answer This From Memory Later)
Why do limits on what an agent can touch make it *more* trustworthy, not less useful?

---

### Observability

#### The Idea
Every meaningful action the agent takes should be visible to you in close to real time. You can only direct what you can see — and when something goes wrong, you should be able to look at a log and understand exactly what happened, rather than discovering the damage after the fact.

#### In Simple Words
It's the difference between watching a contractor work in your house versus coming home to find out what they did. If you watch, you catch the wrong wall getting painted on stroke one, not after the whole room's finished.

#### Real Example
From Agent Factory: a controller runs a "compile the close commentary" task and walks away, but scans the execution trace out of habit on return. One step shows the agent opening a payroll file it had no reason to need — tracing back, a stale folder reference in the rules file had quietly widened its scope weeks earlier. Nothing malicious happened; the habit of scanning caught a drift that had been sitting there unnoticed. For an FDE portfolio story, this kind of catch — "I watch the execution trace, and here's a real drift I found" — is exactly the production-discipline detail that reads well to a hiring panel.

#### The Pitfall
The single biggest source of "the agent did something I didn't expect" is simply never having looked at the execution view. Watch it at least once on every genuinely new task — familiar tasks earn "walk away" status by surviving that first watch.

#### Guiding Question (Answer This From Memory Later)
Why can you only direct what you can actually see the agent doing?

---

### The Four-Phase Workflow

#### The Idea
In production, the seven principles collapse into one repeatable loop: **Explore** (read-only, surface the unknowns), **Plan** (produce and save a structured artifact, review it), **Implement** (small steps, verify and save each one), **Commit** (final verification, update the rules file for next time). Constraints wrap around all four phases as the boundary everything runs inside.

#### In Simple Words
It's the shape of any well-run project: look before you touch anything, write down the plan before building, build it piece by piece checking as you go, then do one final check before calling it done. The phases don't change; only what you're building does.

#### Real Example
From Agent Factory's worked example: reviewing a vendor contract runs Explore (read the contract and redline standard, summarize deviations — no drafting yet), Plan (a numbered redline plan with severity ratings, saved and approved before touching anything), Implement (one clause at a time, each verified against the source, each saved as a numbered version), Commit (a final pass checking every claim is grounded, then assembled into the final memo). For your Timetable project, the same four phases apply to reconciling a semester's worth of room-booking conflicts.

#### The Pitfall
The most common collapse is skipping straight from Explore to Implement — drafting the plan and starting the work in the same response, with no pause for you to review it. Insist on the pause: "Save the plan as a file. Wait for my approval before any implementation."

#### Guiding Question (Answer This From Memory Later)
How do all seven principles from this track collapse into one repeatable four-step loop?

---

## Summary

- **Persisting State in Files**: A rules file carries memory across sessions — conversation is volatile, files are durable.
- **Constraints and Safety**: Scope, connections, and approvals let you earn trust deliberately, climbing an autonomy ladder rather than assuming full freedom.
- **Observability**: Watch the execution trace in real time — you can only direct what you can actually see.
- **The Four-Phase Workflow**: Explore → Plan → Implement → Commit is the loop every principle in this track collapses into.
- **Why Together**: Where Lesson 2's principles make the work happen, these four make the discipline *survive at scale* — they're what lets you genuinely delegate instead of babysitting.

---

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: What does a rules file actually do, and why does its length matter?

**Question 2**: What are the three levers of Constraints and Safety, and what's the rule for climbing the autonomy ladder?

**Question 3**: How does Observability connect to the Four-Phase Workflow — at which phase(s) does watching the execution trace matter most, and why?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector) — *Problem Solving with General Agents: A 90-Minute Crash Course* (Principles 5–7, Part 2: The Four-Phase Workflow)
