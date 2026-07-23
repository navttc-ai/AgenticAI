# Lesson 1: Should You Even Open the Agent?

## Purpose

Most wasted agent time happens before anyone types a word. These 4 concepts are the ten minutes of thinking that separate a clean 25-minute result from an afternoon you can't get back — and they're the difference between "I use AI" and "I know when and why" in an interview.

---

## Guiding Questions

1. How do you decide if a task needs an agent at all — or just a spreadsheet, a search box, or a chatbot?
2. How do you tell whether a repeated task should stay something you solve by hand, or become a permanent worker?
3. Why is "the third time" the right moment to stop and make that decision — not the first, and not a year later?
4. What three lines should you write before you open the agent, so you know when the work is actually finished?

---

## The 4 Concepts

### Gate 1: Task Routing — Not every job is an agent job

#### The Idea
Before opening any AI tool, run the task through two quick checks. First: could a regular tool (spreadsheet, search, find-and-replace) already do this in one step? If yes, stop there. Second: does the task need the tool to *act* on your files and data, or does it just need an *answer*? Answer-only work is a chatbot job. Acting on your real files is an agent job.

#### In Simple Words
Think of it like calling in a specialist. If your kitchen sink is dripping, you don't call an architect — you tighten a bolt yourself. If you just want to know how a dishwasher works, you Google it. You only call the plumber when the job needs someone to actually open the pipes and fix something. Agents are the plumber: powerful, but not for every drip.

#### Real Example
You're triaging a backlog of ShopBot customer messages. "What's the total of this month's refund amounts?" is a spreadsheet job — not even AI. "Explain what a return policy typically covers" needs an answer, nothing of yours gets touched — that's a chatbot job. But "go through these 80 messages and flag which ones are complaints vs. order questions" needs judgment applied to your actual data — that's an agent job. From Agent Factory: this is the exact test Mei runs on her four morning tasks, and two of her four "AI tasks" turn out not to need an agent at all.

#### The Pitfall
People reach for the agent because it's the newest, most exciting tool — Agent Factory calls this the *law of the instrument*: if the only tool you own is a hammer, everything looks like a nail. The fix is running both checks every time, even when the agent feels like the obvious move.

#### Guiding Question (Answer This From Memory Later)
How do you decide if a task needs an agent at all — or just a spreadsheet, a search box, or a chatbot?

---

### Gate 2: Mode 1 vs. Mode 2 — The Three Dials

#### The Idea
Once a task clears Gate 1, decide *how* to handle it: solve it once and walk away (Mode 1), or build a permanent worker that does it every time (Mode 2). Three dials decide which: how often it happens, how same it is each time, and whether it's worth the build effort. A task is only Mode 2 when **all three** dials are turned up.

#### In Simple Words
Picture three light switches: Often, Same, Worth It. If even one switch is off, don't build anything permanent — just solve it by hand this once. Only flip all three on at the same time do you build a machine for it.

#### Real Example
From Agent Factory: David runs day-to-day ops at a 12-person company. Setting up new-hire access happens rarely and differs every time — two dials down, stays Mode 1. But his Monday message summary happens every week, same shape, 25 minutes each time — all three dials up, genuinely Mode 2. For an FDE professional, this is exactly the judgment call behind ShopBot's customer triage: is it the same every week, or does it keep changing shape?

#### The Pitfall
The expensive mistake isn't building a permanent helper too early — that just wastes an afternoon. The quiet, expensive mistake is doing a Mode 2 task by hand for months because no single instance ever felt big enough to stop and notice.

#### Guiding Question (Answer This From Memory Later)
How do you tell whether a repeated task should stay something you solve by hand, or become a permanent worker?

---

### The Rule of Three — When to actually stop and check

#### The Idea
Don't wait to *feel* like a task is worth automating — that feeling never reliably arrives. Instead, use a fixed trigger: the third time you do the exact same task the exact same way, stop and run Gate 2 deliberately.

#### In Simple Words
It's like a rule for restocking your fridge: you don't wait until you're starving to notice you're out of milk — you set a reminder for every Sunday. The third repeat is your reminder to check, not a suggestion to build immediately.

#### Real Example
Agent Factory borrows this from software engineering's "Rule of Three" (Fowler, credited to Don Roberts): the first time you do something, you just do it. The second time, you do it again, even though it repeats. The third time, you stop and decide whether to build the reusable version. For your Timetable project, the third time you manually re-run a scheduling conflict check is your cue to ask: is this now worth scripting?

#### The Pitfall
Building on the *first* repeat is premature — you don't yet know the task's real shape, and you'll likely build the wrong thing. Waiting past the third repeat means you're quietly *being* the unbuilt helper, spending hours you didn't need to.

#### Guiding Question (Answer This From Memory Later)
Why is "the third time" the right moment to stop and make that decision — not the first, and not a year later?

---

### Gate 3: Defining "Finished" — The Three Lines

#### The Idea
Before opening the agent, write three short lines: what it works from (the exact input), what you want at the end (the output's shape, not just its topic), and the one check that tells you it's done and correct. This is choosing the target — not writing the prompt itself.

#### In Simple Words
It's like ordering at a restaurant by pointing at a photo instead of describing a "nice meal." You can't hit a target you haven't named. Vague orders get vague — or wrong — results.

#### Real Example
From Agent Factory, Ana's version for her weekly ShopBot-style triage: **Works from** — the 400 messages in Support from the last 7 days. **Want at the end** — a spreadsheet with sender, date, group, and a one-line summary, plus a one-page note with group counts. **Done when** — every message is in exactly one group, and the group counts add up to 400. That last line is checkable in seconds — by her or by the agent.

#### The Pitfall
The most common failure is a vague "done when": something like "the summary is good." That's an opinion, not a check. If you can't verify it in under a minute, rewrite it until you can.

#### Guiding Question (Answer This From Memory Later)
What three lines should you write before you open the agent, so you know when the work is actually finished?

---

## Summary

- **Gate 1 (Task Routing)**: Sort every task into regular tool, chatbot, or agent — before you open anything.
- **Gate 2 (Three Dials)**: A task is only worth building a permanent worker for when it's often, same, and worth it — all three, no exceptions.
- **The Rule of Three**: The third time you repeat a task the same way is your deliberate trigger to stop and decide, not a feeling to wait for.
- **Gate 3 (Three Lines)**: Write works-from, want-at-the-end, and done-when before opening the agent, so "finished" is a fact you can check, not an opinion.
- **Why Together**: All four run *before* a single prompt is typed — they're the cheap thinking that prevents the expensive mistakes of using the wrong tool, over- or under-building, and chasing a fuzzy target.

---

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: What are the two checks in Gate 1, and what does each one route a task toward?

**Question 2**: What are the three dials in Gate 2, and what must be true of all three for a task to count as Mode 2?

**Question 3**: How do the Rule of Three and Gate 3 work together — why does deciding *when* to check (Rule of Three) matter as much as deciding *what* "finished" means (Gate 3)?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector) — *Is This an Agent Problem? A Crash Course*
