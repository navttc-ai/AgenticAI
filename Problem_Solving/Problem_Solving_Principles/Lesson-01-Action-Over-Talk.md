# Lesson 1: Problem-Solving Principles — Crash Course

## Purpose

These 4 principles are how skilled builders get AI to actually finish work instead of just discussing it. For an FDE positioning yourself in the Gulf market, this is the difference you can point to in an interview: "I don't just prompt — I run a disciplined process."

---

## Guiding Questions

1. Why does AI sometimes only *talk about* doing work instead of doing it?
2. Why does asking for a specific format change the quality of AI's output?
3. Why can a finished-looking AI output still be wrong?
4. Why does breaking a task into small steps prevent big failures?

---

## The 4 Concepts

### Action Over Talk: Treat AI as a doer, not an advisor

#### The Idea
General AI tools like Claude Code, OpenCode, Cowork, and OpenWork aren't chatbots — they read files, write documents, run commands, and keep working until the task is done. From Agent Factory: the first failure mode this fixes is "AI only talks, doesn't act." The fix is simple — give instructions, not questions.

#### In Simple Words
Imagine hiring an assistant and only ever asking them "how would you organize this?" They'll happily explain a method forever and never touch a single folder. Now imagine telling them "organize this folder by date, save the summary to a file." Same assistant, totally different result. AI works the same way — it answers what you ask. Ask a question, get advice. Give an instruction, get a finished result.

#### Real Example
You're building a portfolio case study on your ShopBot project. Instead of asking Claude, "How should I write up this project?" — which gets you a generic essay — you write: "Read every commit message in this repo, pull out the 5 biggest technical decisions, and draft a 400-word case study section for each, saved to `case-study-draft.md`." One produces advice. The other produces a file you can put in front of a hiring manager tomorrow.

#### The Pitfall
Builders new to agentic tools default to asking questions because that's how they'd talk to a colleague. The correction: before sending any prompt, ask yourself "does this end in a file or artifact, or just a paragraph of explanation?" If it's the latter, rewrite it as an instruction.

#### Guiding Question (Answer This From Memory Later)
Why does AI sometimes only talk about doing work instead of doing it?

---

### Structure Over Prose: Give AI a format, not just a paragraph

#### The Idea
From Agent Factory: when precision matters, ask AI for a schema, table, checklist, or template — not a paragraph. AI's output quality rises sharply once you constrain the *format*, because a vague paragraph leaves AI guessing at what "good" looks like, while a defined structure removes the guesswork.

#### In Simple Words
Picture giving someone a blank sheet of paper versus a form with labeled boxes. The blank sheet gets you whatever they feel like writing. The form gets you exactly the information you need, in the same place every time. AI is the same — tell it the shape of what you want, and it fills that shape in.

#### Real Example
You're auditing your teaching materials for a Gulf-region job application. Instead of "summarize my teaching experience," you specify: "For each course I taught, give me a row: course name, audience size, one measurable outcome, and one line linking it to AI/agent skills." That structured table becomes a slide in your portfolio deck. The vague prompt would have given you three unusable paragraphs.

#### The Pitfall
People say "make this cleaner" or "polish this" without defining what "clean" means, so AI drifts toward its own default style. The correction: define the structure — sections, columns, or rules — before asking for content, and the drift disappears.

#### Guiding Question (Answer This From Memory Later)
Why does asking for a specific format change the quality of AI's output?

---

### Verification as a Core Step: Never trust a finished-looking output

#### The Idea
From Agent Factory: AI produces outputs that are *plausible*, which is not the same as *correct*. It can miscount, mis-cite, or write code that looks right but breaks on the third edge case. Verification has to be a deliberate step in the workflow — not a feeling that "this looks fine."

#### In Simple Words
Think of a student who writes a beautifully formatted essay with three made-up statistics. It reads confidently and looks professional — but it's wrong. AI can do the exact same thing. The fix isn't asking AI "is this right?" (it'll usually say yes, because it has the same blind spot that caused the error). The fix is asking it to show its work: "quote the exact source for every claim."

#### Real Example
You're drafting a curriculum map citing Agent Factory content for a client pitch. Rather than trusting the summary AI wrote, you ask: "For every claim in this draft, quote the exact sentence from the SoR that supports it. Flag anything you can't source." Two claims come back unsupported — you catch them before they're in front of a client, not after.

#### The Pitfall
Asking "does this look correct?" feels like verification but isn't — it's the same model re-approving its own blind spot. The correction: demand an independent check — a direct quote, a second model, a test — never a self-review.

#### Guiding Question (Answer This From Memory Later)
Why can a finished-looking AI output still be wrong?

---

### Small, Reversible Decomposition: Break work into checkpoints you can trust

#### The Idea
From Agent Factory: give AI a 12-step task in one prompt and it tends to drift off track by step 5, with no way to catch the mistake until the end. Break the same task into steps, checking and saving after each, and errors get caught while they're still cheap to fix. Rule of thumb: if reversing a change would take more than two minutes, it was too big.

#### In Simple Words
It's the difference between saving a document once every two hours versus every ten minutes. If your computer crashes, losing ten minutes of work stings a little. Losing two hours is a disaster. Small, checkpointed steps mean any single mistake only costs you a small step, never the whole afternoon.

#### Real Example
You're building a lesson-generation pipeline for your curriculum project. Instead of asking AI to "generate all 4 lessons of the curriculum now," you generate one lesson, review it, save it, then move to the next. When Lesson 3 comes out too dense, you only redo Lesson 3 — Lessons 1, 2, and 4 stay intact and already reviewed.

#### The Pitfall
It feels slower to pause and check after every step, so people skip it under time pressure — right before the moment a small early error compounds into a large late one. The correction: treat each pause as insurance, not friction; it's cheaper than a rewrite.

#### Guiding Question (Answer This From Memory Later)
Why does breaking a task into small steps prevent big failures?

---

## Summary

- **Action Over Talk**: Give AI instructions that produce a file or artifact, not questions that produce advice.
- **Structure Over Prose**: Define the format you want (table, checklist, schema) before asking for content — precision goes up sharply.
- **Verification as a Core Step**: A polished-looking answer isn't a checked answer — demand sourced, quoted proof for every claim.
- **Small, Reversible Decomposition**: Break work into steps small enough that any single mistake costs minutes, not hours.
- **Why Together**: These four form the operating discipline behind every effective AI-assisted workflow — act, structure, verify, checkpoint — and they're the exact process an FDE can point to as proof of professional rigor, not just prompting luck.

---

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: What's the difference between asking AI a question and giving AI an instruction — and why does it matter?

**Question 2**: What does "verification as a core step" actually require you to do, beyond re-reading the output?

**Question 3**: How does Structure Over Prose make Small, Reversible Decomposition easier to execute well?

---

**Sources**: Agent Factory System of Record (via SoR MCP connector) — *Problem-Solving Crash Course*, Principles 1–4
