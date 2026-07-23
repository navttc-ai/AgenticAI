# Lesson 2: Making the Agent Actually Do the Work

## Purpose

Same tool, same task, two very different outcomes: one person finishes in 25 minutes with a clean result, another spends 90 minutes in a cluttered mess. These 4 concepts are the whole difference — and they're what makes your agent output look like production work, not a lucky prompt.

---

## Guiding Questions

1. Why does asking AI a question get you advice, while giving it an instruction gets you a finished result?
2. Why does the *format* you ask for change the quality of what you get back, even when the content is the same?
3. Why is "looks right" the most dangerous phrase in working with AI output?
4. Why does one giant prompt tend to fail worse than the same task broken into small steps?

---

## The 4 Concepts

### Action Over Talk: Bash is the Key

#### The Idea
A chatbot answers your questions. An agent takes action — it reads files, writes documents, runs commands, and keeps going until the task is done. The first discipline is treating AI as a doer, not an advisor: give it instructions that produce a finished artifact, not questions that produce a paragraph of suggestions.

#### In Simple Words
It's the difference between asking a coworker "how should I organize these files?" and saying "organize these files into folders by client, and tell me when you're done." The first gets you a lecture. The second gets you a done task.

#### Real Example
From Agent Factory: asking "How should I organize my notes from last week?" gets a long explanation and nothing actually organized. The fix — "Read every file in the `notes/` folder, pull out the action items and who's responsible, save the result to `weekly-summary.md`, sorted by person" — gets a finished file. For your Timetable project, this is the gap between "how should I resolve scheduling conflicts?" and "read the conflict list, resolve each using the priority rules in `rules.md`, and save the updated schedule."

#### The Pitfall
The beginner mistake is asking questions out of habit, even with a capable agent in front of you. If you catch yourself typing something that starts with "how should I..." — stop, and ask instead: can this become an instruction that produces a file?

#### Guiding Question (Answer This From Memory Later)
Why does asking AI a question get you advice, while giving it an instruction gets you a finished result?

---

### Structured Output: Code as Universal Interface

#### The Idea
When precision matters, ask for a defined shape — a table, schema, or checklist — instead of a paragraph. The more specific the format you request, the less the agent has to guess. For genuinely complex tasks (crossing file formats, needing real computation), the agent should write code rather than just talk through steps.

#### In Simple Words
A vague request is like asking someone to "make dinner nice." A structured request is a recipe with exact ingredients and steps. The recipe gets you the same dish every time; "make it nice" gets you something different every time, and usually not what you meant.

#### Real Example
From Agent Factory: Sarah had 3,000 trip photos scattered across a phone, camera, and backup drive. Three photo apps each did *part* of what she wanted; none did the combination. One structured paragraph to an agent — organize by country/city using location data, rename by date, dedupe by actual image content — and the agent wrote a short program that did all of it in 15 minutes. For an FDE portfolio piece, this is the same move as asking for "a table with columns: candidate name, required-qualification match (yes/no), preferred-qualification count, recommendation" instead of "tell me who looks good."

#### The Pitfall
The common failure is saying "make this cleaner" or "polish this" without defining what clean means. Without a structure, the agent drifts — put the rules in the format you request, not in a vague adjective.

#### Guiding Question (Answer This From Memory Later)
Why does the *format* you ask for change the quality of what you get back, even when the content is the same?

---

### Verification as a Core Step

#### The Idea
A finished-looking output is not the same as a verified one. Models produce answers that are *plausible*, which isn't the same as *correct* — they can confidently miscount, mis-cite, or write code that looks right but silently fails. Verification has to be a deliberate step in the workflow, not something you assume happened.

#### In Simple Words
It's like proofreading your own essay right after writing it — you miss your own typos because your brain already knows what it "meant" to say. You need someone else, or a different pass, to actually catch the errors. Asking the same AI "is this correct?" is like asking the person who wrote the wrong answer to grade their own work.

#### Real Example
From Agent Factory: a manager asks for last quarter's regional sales. The agent says "$4.2 million." Finance checks the same data and gets $3.8 million. Asked why, the agent confidently gives a *third* number: $4.5 million. Three different answers from the same data — because nothing forced a check. For a ShopBot or Timetable deliverable heading into a review, the fix is a verification pass: "For every number in this report, quote the exact row in the source spreadsheet that supports it. Flag anything you can't find."

#### The Pitfall
"Looks right" is the failure mode. If the agent says "everything checks out" without quoting specific source data, that's not real verification — it's the agent re-reading and approving its own work.

#### Guiding Question (Answer This From Memory Later)
Why is "looks right" the most dangerous phrase in working with AI output?

---

### Small, Reversible Decomposition

#### The Idea
Break big tasks into small steps. Finish one, check it's correct, save your progress, then move on. The rule of thumb: if reversing a change would take more than two minutes, the change was too big.

#### In Simple Words
It's the difference between saving a document every paragraph versus writing six pages and hoping nothing goes wrong before you hit save. If your computer crashes after paragraph one, you lose a sentence. If it crashes after page six, you lose everything.

#### Real Example
From Agent Factory: in 1998, someone at Pixar accidentally deleted two years of *Toy Story 2* production files — saved only because one employee happened to have a personal backup at home. The lesson: saving progress can't be something you remember to do; it has to be built into the process. For a Timetable rebuild, that means: draft one department's schedule, verify it against the constraint list, save it, *then* move to the next department — not generate all eight departments in one shot and discover the error in department three after the fact.

#### The Pitfall
Giving an agent a 12-step task in one message means it tends to drift by step 5, with no way to catch the mistake until the very end. The fix is checkpoints: show me what you did, verify it, save it, wait for my OK — one step at a time.

#### Guiding Question (Answer This From Memory Later)
Why does one giant prompt tend to fail worse than the same task broken into small steps?

---

## Summary

- **Action Over Talk**: Give instructions that produce a finished artifact, not questions that produce advice.
- **Structured Output**: Specify the shape you want — table, schema, checklist — and let the agent write code when the task needs real computation.
- **Verification as a Core Step**: Every meaningful output needs an independent check; "looks right" is not correct.
- **Small, Reversible Decomposition**: Break work into small, checked, saved steps so one mistake costs a step, not an afternoon.
- **Why Together**: These are the four "doing" principles, and they build on each other in order — act, then structure the result, then verify it, then keep the whole process recoverable as you go.

---

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: What's the concrete difference between an "advice" prompt and an "instruction" prompt, and what does each produce?

**Question 2**: What makes verification "real" rather than just the agent re-approving its own work?

**Question 3**: How do Structured Output and Small, Reversible Decomposition work together on a multi-step deliverable — why does defining the shape *and* breaking it into steps matter more than doing just one of the two?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector) — *Problem Solving with General Agents: A 90-Minute Crash Course* (Principles 1–4)
