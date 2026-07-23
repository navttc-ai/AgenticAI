# Lesson 4: When to Stop Solving and Start Building

## Purpose

You don't build an AI worker from a blank page — you promote a solution you've already proven by hand. These 3 concepts are the bridge from solving problems one at a time to manufacturing something that solves them without you, and they're exactly the story that turns a portfolio project into a hiring conversation.

---

## Guiding Questions

1. How do you know a task has actually earned the right to become a permanent worker, rather than just being repeated?
2. What four things you already did by hand get promoted into a worker, and why isn't this starting from scratch?
3. Why does "who is this worker for?" decide everything about how you build it?

---

## The 3 Concepts

### The Crossing Signal

#### The Idea
A task is ready to become a permanent worker only when two things are both true: Gate 2 says Mode 2 (often, same, worth it — from Lesson 1), **and** you've solved it cleanly enough times by hand that the method has stopped changing. The second half is the one people skip, and skipping it is what causes a week of build effort to produce the wrong worker.

#### In Simple Words
It's like a recipe you've cooked five times before writing it down for someone else to follow. The first two times you're still figuring out the right amount of salt. By the fifth, the recipe has settled — *that's* when it's safe to hand off, not before.

#### Real Example
From Agent Factory: Ana's weekly customer-message triage passed Gate 2 (often, same, worth it) almost immediately — but she kept solving it by hand for weeks anyway. Each week she caught a new edge case: a message in a language she hadn't planned for, a refund amount that needed a judgment call. Building the worker on week one would have missed all of that. For a ShopBot or Timetable task, the test is simple: the last three times you did this, did you do it the same way? If yes, it's ready. If you're still discovering new wrinkles each time, it isn't yet — and that's useful information, not a delay.

#### The Pitfall
The instinct is to build the worker the moment a task *feels* repetitive. Agent Factory is explicit: the repetitions aren't wasted time before automating — they're the research that tells the worker what to actually do. Cross when the learning slows down, not before.

#### Guiding Question (Answer This From Memory Later)
How do you know a task has actually earned the right to become a permanent worker, rather than just being repeated?

---

### The Four Promotions

#### The Idea
Manufacturing a worker isn't inventing something new — it's promoting four things you already built by hand in Mode 1 into permanent, automatic forms: your brief becomes a **spec**, your own eyeball check becomes an **eval**, you-in-the-loop becomes designed **exits** (when to stop and call a human), and your open session becomes a **runtime** that keeps running without you.

#### In Simple Words
Imagine training a new employee by writing down everything you'd normally just remember to do yourself: the instructions you'd give (spec), the way you'd double-check their work (eval), the specific situations where they should stop and ask you instead of guessing (exits), and making sure they show up to work even when you're not there to remind them (runtime). None of it is new information — it's just written down and made permanent.

#### Real Example
From Agent Factory's worked-through example, Ana's crossed Monday task: **Brief → spec** — "every Monday, read new Support messages, sort into exactly one of four groups, write a one-page summary." **Check → eval** — twelve past messages she'd already sorted by hand, used to test the worker before it touches real mail. **You → exits** — if a message is in an unhandled language, or requests a refund above a set limit, the worker flags it and pings Ana instead of guessing. **Session → runtime** — it runs every Monday morning on a small always-on machine, remembering what it already processed. None of these four were invented on crossing day — they're two months of Mondays, made permanent. This is a genuinely reusable portfolio artifact: your own ShopBot triage, once it's crossed, is this exact table filled in with your own answers.

#### The Pitfall
People treat the exits (Promotion 3) as an afterthought, since the routine part already works. Agent Factory is direct about this: the routine is the easy part your Mode 1 method already handles — the real work and the real risk live entirely in designing what happens at the edges.

#### Guiding Question (Answer This From Memory Later)
What four things you already did by hand get promoted into a worker, and why isn't this starting from scratch?

---

### Task vs. Asset — The Fork

#### The Idea
Solving a task by hand is labour as a **task**: you pay the time every single time you do it. Promoting it into a worker turns that same labour into an **asset**: you pay the time once, and it keeps producing results while you do something else. Once you decide to build, one question forks the path: who is the worker for? For you alone → a lightweight personal harness. For an organization → a full Digital FTE, built and governed more rigorously.

#### In Simple Words
It's the difference between renting and owning. Renting (solving by hand) means you pay every month, forever. Owning (building the worker) means a bigger cost once, and then it's yours. And whether you buy a small apartment or a whole office building depends entirely on who's going to live there — that's the fork.

#### Real Example
From Agent Factory: Diego spends roughly a hundred hours a year hand-solving his version of the same weekly task Ana crossed. Ana spent a few hours building hers once; her time-spent line goes nearly flat after that, while Diego's keeps climbing every week, forever. For your own portfolio, this is the fork worth naming out loud: your ShopBot triage worker, built for one seller, might stay a lightweight personal harness. But if you're pitching it as something a whole team of sellers could rely on, that's a genuine Digital FTE conversation — governed, reviewed, and built to scale.

#### The Pitfall
Treating "owning a personal harness" as a third mode between Mode 1 and Mode 2 is a common confusion. It isn't — it's the exact same build-a-worker activity, just scaled down to one person. *Mode* asks solve-once-versus-build-to-last; *ownership* asks yours-versus-an-organization's. They're two separate questions that never collide.

#### Guiding Question (Answer This From Memory Later)
Why does "who is this worker for?" decide everything about how you build it?

---

## Summary

- **The Crossing Signal**: Mode 2 (from Gate 2) plus a method that's stopped changing across repeated hand-solves — both required, not either alone.
- **The Four Promotions**: Brief→spec, check→eval, you→exits, session→runtime — you're promoting proven work, not building from nothing.
- **Task vs. Asset / The Fork**: Hand-solving costs you every time; a worker costs once — and who it's for decides personal harness versus Digital FTE.
- **Why Together**: This is the handoff lesson — it takes everything from Lessons 1–3 (your gates, your principles, your discipline) and answers what happens once a task has genuinely outgrown being solved by hand.

---

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: What are the two conditions that together make up the Crossing Signal, and why isn't Gate 2 alone enough?

**Question 2**: Name the Four Promotions in order and, for each, what it replaces from Mode 1.

**Question 3**: How does the Crossing Signal from this lesson connect back to Gate 2 and Gate 3 from Lesson 1 — what work from Lesson 1 does a "crossed" task actually reuse?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector) — *From One-Off to Worker: The Handoff to Manufacturing*
