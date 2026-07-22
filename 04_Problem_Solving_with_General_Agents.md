# Problem Solving with General Agents: Complete Test Prep Lesson

**Target Audience:** Student preparing for test on this topic (Next Saturday)  
**Content Source:** Agent Factory System of Record  
**Estimated Reading Time:** 55-65 minutes  
**Study Focus:** Understanding the Seven Principles + practical application

---

## OPENING HOOK: The 25-Minute vs. 90-Minute Gap

Two students get the same assignment: read through five research papers, pull out the key findings, and write a one-page comparison summary.

**Student A:** Finishes in 25 minutes with a clean, verified summary. Done.

**Student B:** Spends 90 minutes going back and forth. The conversation gets cluttered. The AI starts making mistakes. They have to start over. Still not done.

Same AI tool. Same papers. Same task.

**What's the difference?** Student A understood something Student B didn't. It wasn't about being smarter or faster. It was about **seven principles.**

This lesson teaches those seven principles—the difference between an agent that wanders and one that lands. The difference between 25 minutes and 90 minutes. The difference between a result you can ship and a result you have to scrap.

Here's the thing: these principles are not new. They're old. Really old. The terminal (bash), files, Git, SQL—these tools have been around for decades. They survived because they work. **AI doesn't invent its own way of working. It uses the same tools that have existed for decades.** Understanding what makes those tools powerful is what makes you powerful when you're directing an agent.

---

## CORE CONCEPT: What Mode 1 Is (and Why It Matters)

### In Plain English

**Mode 1** is when you:
1. Open an agent
2. Point it at a task
3. Work with it until the task is done
4. Take the result
5. Close the session

Nothing permanent is left behind. The next problem starts fresh.

Most work you'll ever do with an agent will be Mode 1. Most of the time, that's the right answer. Mode 1 is "solve it once." There's a different thing called Mode 2 (build a permanent worker), but that's for tasks you do again and again. For a one-off? That's Mode 1.

### Real-World Analogy

**Mode 1:** You hire a consultant for one specific project. They come in, solve the problem, deliver the result, and leave. Next project, you hire someone different (or the same consultant, starting fresh). You're not building a permanent team member.

**Mode 2:** You hire a full-time employee. They learn your company, remember your preferences, adapt when rules change. They show up every day for years.

Agent work is the same. Mode 1 is the consultant. Mode 2 is the employee.

### The Technical Definition (From Agent Factory)

From Agent Factory: "Mode 1 uses a general agent to solve a problem inside the session."

A **general agent** is an AI that does not just answer questions—it *acts*. It opens your files, reads them, writes documents, runs commands, uses other apps to get tasks done.

**Mode 1** is you directing that agent for one session, one task, done.

### Why This Matters

Because 80% of real work is Mode 1. You'll spend months in Mode 1 before you ever build a Mode 2 worker. If you only know Mode 2, you're skipping the foundation. This lesson is about **solving Mode 1 problems well**.

---

## THE SEVEN PRINCIPLES: The Framework

Before you start any Mode 1 problem-solving session, you need to know these seven principles. They are not suggestions. They are the operating discipline of the session.

Here's what they solve:

| Principle | Failure Mode It Prevents |
|-----------|-------------------------|
| **1. Bash is the Key** | "Agent only talks, doesn't act" |
| **2. Code as Universal Interface** | "Prose request keeps getting misread" |
| **3. Verification as Core Step** | "Output looks right but breaks in production" |
| **4. Small, Reversible Decomposition** | "One big change nuked an afternoon of work" |
| **5. Persisting State in Files** | "Agent forgets what we decided yesterday" |
| **6. Constraints and Safety** | "Agent touched files I didn't authorize" |
| **7. Observability** | "Don't know what the agent actually did" |

Think of them as layers, one inside the next:

```
┌──────────────────────────────┐
│  Your Goal (Top)             │
├──────────────────────────────┤
│  P1-P5: Working Disciplines  │
├──────────────────────────────┤
│  P6: Constraints (What it    │
│      can touch)              │
├──────────────────────────────┤
│  P7: Observability (What it  │
│      actually did)           │
└──────────────────────────────┘
```

Each principle depends on the ones below it. A good prompt fails inside bad context. Good context fails inside a bare harness (no constraints). A good harness sits idle without observability.

---

## KEY PRINCIPLE 1: Bash Is the Key (Action Over Talk)

### What This Means

**"Bash"** is the terminal—the black-screen text interface. When an agent runs Bash, it's typing commands on your computer, the same ones you'd type. The agent has full keyboard access, through commands instead of clicks.

For Cowork/OpenWork users: the principle is the same on a different surface. Instead of typed commands, you see step cards ("Read 3 files," "Ran a script"). Either way: **the agent acts on your computer, you watch it act.**

### The Failure Mode

Most people start by asking questions:
- "How should I organize my notes?"
- "What's the best way to structure this project?"
- "Can you explain how to categorize these files?"

The agent gives a long explanation. Your notes are still disorganized. You got advice when you needed action.

### The Fix: Instruction vs. Question

| **Asking for Advice (Weak)** | **Giving an Instruction (Strong)** |
|-----|-----|
| "How should I organize my notes from last week?" | "Read every file in the `notes/` folder. For each file, pull out the action items and who is responsible. Save to `weekly-summary.md`, sorted by person." |
| Result: A paragraph of suggestions | Result: A finished file |

**The rule:** Brief the hands, not the brain. Tell the agent what to do and what to work with. Let it figure out the how.

### Real-World Example: E-Commerce

**Weak prompt:** "How should I organize my customer data?"
(Agent explains options, but doesn't touch anything)

**Strong prompt:** "Read every email in `customer-emails/`. For each email, extract: customer name, email address, product purchased, purchase date. Save to `customer-database.csv`. Show me duplicates by email address."
(Agent reads 200 emails, extracts info, finds duplicates, saves the file in 5 minutes)

### 🤔 Stop & Think

**Question 1:** What's the difference between "asking for advice" and "giving an instruction"? Why does one result in explanation and the other result in action?

**Question 2:** In your own work, can you identify a task where you've been asking for advice when you should ask for action?

**Question 3:** Why do you think Agent Factory calls this principle "Bash is the Key" rather than just "Action Over Talk"?

*Suggested reflection time: 5 minutes*

---

## KEY PRINCIPLE 2: Code as Universal Interface (Structure Matters)

### What This Means

When a task is complex—reading files in three different formats, combining data from multiple sources, applying custom rules—**the agent writes code to handle it**.

But there's a second part: **the format you ask for matters as much as what you ask for.** A vague paragraph is confusing. A table, a template, or a checklist is crystal clear.

### Why This Is Powerful

Regular apps only do what they were built to do. If your task doesn't fit the features, you're stuck. When an agent can write code, it can solve problems no existing app was designed for.

Here are five things code lets an agent do:

1. **Precise thinking** — Read 100 expense receipts, categorize them, calculate totals to the exact cent
2. **Workflow orchestration** — "If file is X, do Y; if file is Z, do W"—all in one pass
3. **Organized memory** — Save intermediate results to files so the agent can pick up later
4. **Universal compatibility** — Read a spreadsheet, PDF, emails, and screenshots in the same session, combine into one output
5. **Instant tool creation** — Build something that didn't exist before

### Real-World Example: Education

**The problem:** You have a class of 35 students. Their grades are in an Excel file. Their attendance is in a Google Sheet. Their assignment submissions are on Google Classroom. You need one master report showing: name, grade, attendance, assignments submitted.

**The pre-agent solution:** Download Excel, download Google Sheet, open Google Classroom, manually match names across all three, build the report. Takes 2 hours.

**The agent solution:** "Read grades from `grades.xlsx`, attendance from the Google Sheet (link here), assignments from Google Classroom. Merge by student name. Create one report with columns: name, grade, attendance %, assignments submitted. Save to `class-summary.csv`."

The agent writes code that:
- Reads all three formats (Excel, Google Sheet, web API)
- Matches names across all three
- Creates the merged report

Done in 3 minutes. The tool didn't exist before.

### The Two Things You Do

**1. Define the problem (tell AI what you want)**
- For complex tasks: "Describe what you want in this order: input, process, output"
- Example: "I have 15 receipts in three formats. Extract date and amount from each. Save to a table with columns: date, amount, source format. Flag duplicates by amount + date."

**2. Check the result (verify the output)**
- You don't write code, but you read it well enough to spot mistakes
- "Is the formula reading the right data?" "Did it filter anything incorrectly?"

### 🤔 Stop & Think

**Question 1:** Why is "describe the structure you want" more powerful than "describe the problem"? Give an example from something you do regularly.

**Question 2:** A photo collection needs organizing by date and location. A regular app has preset categories. An agent can write custom code. What's the difference in what's possible?

**Question 3:** What's the difference between Bash (Principle 1) and Code (Principle 2)? When would you need one vs. the other?

*Suggested reflection time: 5 minutes*

---

## KEY PRINCIPLE 3: Verification as Core Step (Trust But Check)

### What This Means

A finished-looking output is not the same as a correct output. **Models produce outputs that are plausible, not necessarily correct.** They confidently mis-cite paragraphs that don't exist, miscount items, and produce code that compiles cleanly while silently failing on edge cases.

**Verification must be a step in the workflow, not an afterthought.**

### The Failure Mode

A finished-looking memo with a "$5 million budget" looks correct. Turns out the actual budget is $3 million, and your whole plan depends on the wrong number. The output looked right. It wasn't.

### How Verification Works

**For documents/memos:** For every factual claim (numbers, quotes, references), find where it came from and quote the exact source. If you can't find the source, flag it.

**For code:** Run tests. Check that the output matches what you expected. Don't just assume it's correct because it looks clean.

**Key rule:** The agent that produced the output is the worst possible verifier. It has the same blind spots. You need an *independent* check—your own reading, a different model, a test, or a database constraint.

### Real-World Example: E-Commerce

You want to create a customer summary saying:
- "We have 347 customers"
- "Average order value is $127"
- "Repeat rate is 34%"

**Without verification:** These numbers go into your investor deck.

**With verification:** You ask the agent: "For each number in the summary, quote the exact data that supports it. Find it from the source files. If you can't find the source, flag it as unsupported."

Result: "We have 347 customers" ← Verified from `customer-database.csv`. "Average order value is $127" ← Verified from `order-history.csv`, exact calculation shown. "Repeat rate is 34%" ← **FLAGGED UNSUPPORTED** — couldn't find exact rows that prove this.

Now you know to investigate the repeat rate before sending the deck.

### 🤔 Stop & Think

**Question 1:** Why is verification important even if AI gets more accurate over time?

**Question 2:** What's the difference between "verify the code works" vs. "verify the code does what you want"? Which is harder?

**Question 3:** In your field/work, what would happen if a number was wrong by 10%? 50%? How careful should verification be?

*Suggested reflection time: 5 minutes*

---

## KEY PRINCIPLE 4: Small, Reversible Decomposition (Don't Blow Up)

### What This Means

**Break big tasks into small steps.** Finish one step, check it, save your progress, then move to the next. If something goes wrong, you only lose the last small step—not an entire afternoon of work.

**The rule of thumb:** *If reversing the change would take more than two minutes, the change was too big.*

### Why This Matters

When you give an agent 12 steps in one message, it tends to drift off track by step 5. You have no way to catch it until the end. When you give those same 12 steps one at a time, checking each before moving on, the result is much better.

### Real-World Example: Education

**Bad approach:** "Write a 10-page report on educational technology trends. Include history, current landscape, case studies, future predictions, and recommendations."

AI writes all 10 pages in one go. By page 8, it's forgotten the tone from page 1. The flow doesn't work. You have to rewrite half of it.

**Good approach:**
1. AI writes page 1-2 (history) → You check → OK to continue
2. AI writes page 3-4 (current landscape) → You check → Verify it references history correctly
3. AI writes page 5-6 (case studies) → You check → Make sure each case shows a real example
4. AI writes page 7-8 (future predictions) → You check
5. AI writes page 9-10 (recommendations) → Final check

Each section is short enough for you to read. If section 3 drifts, you catch it before sections 4-10 are built on top of it. Total time: 45 minutes instead of 90.

### How to Save Your Progress

**In Claude Code/OpenCode:** Use Git commits after each working step. If something breaks, you can revert to any saved point.

**In Cowork/OpenWork:** Save numbered versions (`memo-v1.md`, `memo-v2.md`, `memo-v3.md`). If you need to go back, just copy the old version back.

### 🤔 Stop & Think

**Question 1:** Why does breaking work into steps actually save time, even though it seems like you're doing extra work?

**Question 2:** What's the difference between "save after each step" and "save the final version"? Why does one prevent disaster and the other doesn't?

**Question 3:** Can you think of a task where you've lost work because you didn't save progress in small steps? What would small steps have looked like?

*Suggested reflection time: 5 minutes*

---

## KEY PRINCIPLE 5: Persisting State in Files (Memory Lives in Files)

### What This Means

**The conversation is volatile. The filesystem is durable.**

Every session, the agent forgets everything from last time—decisions, conventions, the plan you made yesterday. You start from scratch explaining things.

Solution: **Anything worth remembering goes into a file.**

Conventions, decisions, project glossaries, plans, assumptions—save them to files. The agent can read them at the start of the next session.

### Real-World Example: E-Commerce

**First session:** You define how to categorize products (Electronics, Clothing, Home, etc.)

**Without Principle 5:** Next session, you re-explain the categories. And the session after that. And the session after that.

**With Principle 5:** You save a file called `product-categories.md`:

```
# Product Categories

- **Electronics:** Computers, phones, cables, accessories
- **Clothing:** Shirts, pants, dresses, jackets (no shoes)
- **Shoes:** Boots, sneakers, sandals
- **Home:** Furniture, bedding, kitchen items
- **Other:** Everything else
```

Every future session, you tell the agent: "Read `product-categories.md` first. Use these categories for everything."

The categories stick. The agent doesn't forget.

### What Gets Saved to Files

- **Decisions:** "We decided to track these 5 metrics"
- **Rules:** "Do not delete anything without asking first"
- **Conventions:** "Use this folder structure" / "Name files this way"
- **Context:** "This project is for [client/purpose]"
- **Status:** "Current progress: X done, Y to do"

### 🤔 Stop & Think

**Question 1:** What's the difference between "the agent remembering" vs. "you telling the agent to read a file"?

**Question 2:** If you're solving the same type of problem repeatedly (but Mode 1 each time), what should you save to files?

**Question 3:** Can you think of decisions you've re-explained to people multiple times? What would happen if that was a file instead?

*Suggested reflection time: 5 minutes*

---

## KEY PRINCIPLE 6: Constraints and Safety (Guardrails)

### What This Means

These AI tools can read, edit, and delete your files. They can run commands on your computer. **Before you give AI access to anything, make sure you understand what it's allowed to touch.**

**Start by making AI ask your permission before every action.** Don't let it access everything at once. Permission mistakes can delete important files or share private information.

### Real-World Example

**Dangerous:** "Organize my entire computer. Move files around, delete duplicates, clean up everything."

**Safe:** "Read-only: Look at my Downloads folder and tell me what's there. Do not move or delete anything. Wait for my OK before touching any files."

### The Enforcement

Always start with minimal permissions:
- Read-only access to specific folders
- AI must ask before writing or deleting
- AI cannot access anything it doesn't need
- If the task needs new access, add it explicitly

### 🤔 Stop & Think

**Question 1:** What could go wrong if you gave an agent permission to "organize your computer" without restrictions?

**Question 2:** Why should you start restrictive and expand, rather than start open and restrict later?

**Question 3:** In your own work, what files would you never want an agent to access unsupervised?

*Suggested reflection time: 3-4 minutes*

---

## KEY PRINCIPLE 7: Observability (Transparency)

### What This Means

**You need to be able to see what the agent actually did.** No black boxes. No surprises.

In Claude Code/OpenCode: See the commands as they run. See the diff of what changed. In Cowork/OpenWork: See step cards showing each action.

If something goes wrong, you can see exactly where it went wrong.

### Real-World Example

**Bad observability:** AI says "Done!" but you don't know what it actually did. Is the file updated? Which lines changed? Did it delete anything?

**Good observability:** You see:
```
$ ls -lh customer-data/
$ wc -l customer-data/raw.csv
$ head -20 customer-data/raw.csv
[changes shown in green/red]
$ git diff --stat
```

You watched it happen. You can verify each step.

### 🤔 Stop & Think

**Question 1:** Why is "seeing what happened" as important as "it being correct"?

**Question 2:** If AI claimed to fix a bug, what would you need to see to actually verify it fixed it?

*Suggested reflection time: 3 minutes*

---

## SYSTEMS THINKING: How All Seven Connect

Think of them as nested layers:

**Core (P1-P5):** Working disciplines
- Action over talk
- Structure matters
- Verify everything
- Small, reversible steps
- Persistent memory

**Operational (P6-P7):** Constraints and observability wrap the core
- What the agent can touch
- What it actually did

**Why all seven matter:** A good prompt fails inside bad context. Good context fails inside a bare harness (no constraints). A good harness sits idle without observability.

You need all of them. Skip one and the whole thing breaks.

---

## REAL-WORLD APPLICATION: The Full Loop (E-Commerce)

Let's walk through a real Mode 1 problem using all seven principles.

### The Problem

You're an e-commerce seller. You have 847 orders from the past month in a CSV file. You need to:
1. Categorize orders (returned, completed, pending)
2. Calculate average order value per category
3. Identify your top 5 customers by revenue
4. Find any customers who bought multiple times in the same week
5. Create a summary report

**Time if done manually:** 3-4 hours
**Time using an agent + the Seven Principles:** 20 minutes

### The Solution (Applying Each Principle)

**Principle 1 (Action):** "Read the order data and produce artifacts—files, numbers, lists. Don't just explain."

**Principle 2 (Structure):** "Save results to a structured table with columns I can understand."

**Principle 3 (Verification):** "Before showing me the final numbers, verify them against the source data. Show me the verification."

**Principle 4 (Decomposition):** "Do one task at a time. Show me the result. Then move to the next."

**Principle 5 (Files):** "Save each result to a separate file. If I need to redo step 3 tomorrow, read the step 2 file and continue from there."

**Principle 6 (Constraints):** "Read-only access to the orders CSV. Do not touch anything else in my folder."

**Principle 7 (Observability):** "Show me what files you're reading, what calculations you're running, and what you're saving."

### The Results

- **Category breakdown:** 412 completed, 198 pending, 237 returned → verified against source data
- **Average order value:** Completed: $87.50, Pending: $64.20, Returned: $52.10
- **Top 5 customers:** Names, emails, total revenue per customer
- **Repeat customers:** 34 customers bought multiple times in the same week
- **Summary report:** Ready to send to your team

All saved to files. All verified. All done in one focused session.

---

## PRACTICAL NEXT STEPS: Test Prep This Week

**Action 1: Memorize the Seven Principles (30 minutes)**
- Write them out: Bash, Code, Verify, Decompose, Files, Constraints, Observability
- For each one, write one sentence: "This principle prevents ___ failure mode"
- Outcome: You can explain each principle without notes

**Action 2: Understand the Failure Modes (45 minutes)**
- Go through the table at the top of this lesson
- For each failure mode, write: "This happens when..." and "To fix it, you should..."
- Outcome: You can identify which principle is missing when something goes wrong

**Action 3: Practice Instruction Writing (1 hour)**
- Take 3 tasks you currently do
- Rewrite each as an instruction (not a question)
- Use the "strong prompt" format from Principle 1
- Outcome: You can brief an agent clearly

**Action 4: Build Your Own Example (1.5 hours)**
- Pick a real problem from your life or work
- Write it out: the input, the process, the output you want
- Identify which principles apply
- Outcome: You have a personal example to cite on the test

---

## FAQs & MISCONCEPTIONS

**Q: Isn't this just prompt engineering?**

A: No. Prompt engineering is how you talk to AI. The Seven Principles are the *discipline* of using AI to solve problems. Good prompts fail inside bad context. Good context fails inside a bare harness. The whole stack matters, not just the words.

**Q: Do I need to know how to code to use these principles?**

A: No. You don't write the code; the agent does. You need to be able to *read* code well enough to spot obvious mistakes. "Is it reading the right file?" "Is the filter wrong?" That's enough.

**Q: What's the difference between Mode 1 and Mode 2?**

A: Mode 1 is "solve it once." You open an agent, solve the problem, ship the result, close. Done. Mode 2 is "build a permanent worker" that runs every week without you. This lesson teaches Mode 1. Most of your work will be Mode 1.

**Q: Why does the lesson emphasize "small steps" so much?**

A: Because one big change is harder to catch. Errors compound. By step 3, it's hard to know if the mistake came from step 1 or step 2. Small reversible steps make it obvious where errors are. They also let you change direction if step 2 tells you step 1 was wrong.

**Q: What if the agent ignores the principles?**

A: Tell it explicitly. "Do not explain. Just act." "Show me step 1 only, then stop." "For each claim, quote the source." "Save the result to a file after each step." The agent will follow the rule.

**Q: How long should a session be?**

A: There's no fixed time. It's done when the result is verified and saved. Most Mode 1 problems: 20 minutes to 2 hours. Anything longer, you're probably not decomposing into small enough steps.

---

## SUMMARY: The One-Page Takeaway (Study Sheet)

### The Seven Principles

1. **Bash is the Key** — Action, not explanation. Brief the hands.
2. **Code as Interface** — Structure matters. Define the format you want.
3. **Verification as Core** — Check every factual claim. Don't trust the output.
4. **Decomposition** — Small steps, each verified, then saved before moving on.
5. **State in Files** — What you need to remember goes into files, not conversation.
6. **Constraints** — Start restrictive. Give the agent only the access it needs.
7. **Observability** — See what the agent did. No black boxes.

### The Failure Modes They Prevent

| Principle | Prevents |
|-----------|----------|
| P1 | "Agent only talks, doesn't act" |
| P2 | "Prose request keeps getting misread" |
| P3 | "Output looks right but breaks later" |
| P4 | "One big change nuked an afternoon" |
| P5 | "Agent forgets what we decided yesterday" |
| P6 | "Agent touched files I didn't authorize" |
| P7 | "Don't know what the agent actually did" |

### Mode 1 vs. Mode 2

| Aspect | Mode 1 (Solve Once) | Mode 2 (Build Worker) |
|--------|-------|---------|
| Task | One-off problem | Repeated, regular task |
| Duration | Session-based | Weeks/months/years |
| Ownership | You direct each time | Worker runs on its own |
| Example | "Organize these 50 photos" | "Organize photos every week automatically" |

### The Workflow

**Phase 1: Constraints** → Define what the agent can touch
**Phase 2: Input** → Give it the files/data it needs
**Phase 3: Instruction** → Brief the hands, not the brain
**Phase 4: Execution** → Watch it happen (observability)
**Phase 5: Verification** → Check every claim/result
**Phase 6: Saving** → Persistent state to files
**Phase 7: Iteration** → Next step builds on verified result

### For the Test: Master These

- [ ] Can you name and describe all Seven Principles?
- [ ] Can you identify which principle prevents which failure mode?
- [ ] Can you rewrite a vague question as a clear instruction?
- [ ] Can you explain why "small steps" save time?
- [ ] Can you distinguish Mode 1 from Mode 2?
- [ ] Can you explain why "action over explanation" matters?
- [ ] Can you describe what happens if you skip verification?

---

## KEY TEST QUESTIONS YOU MIGHT GET

**Understanding Questions:**
1. Name the Seven Principles in order.
2. What does "Bash is the Key" mean? (Hint: not about programming)
3. Why does the lesson say "the filesystem is durable but the conversation is volatile"?

**Application Questions:**
4. You have receipts in three formats (photos, PDFs, screenshots). Which principle lets an agent handle all three at once?
5. You write a memo with five numbers. How would you verify each one?
6. A task takes 90 minutes in one big prompt but 30 minutes in five small steps. Why?

**Distinction Questions:**
7. What's the difference between a "question" and an "instruction"? Give an example.
8. Mode 1 vs. Mode 2: When would you use each?
9. Verification vs. Observability: What's each one for?

**Failure Mode Questions:**
10. "My agent touched files I didn't want it to touch." Which principle was missing?
11. "The output looks correct but turns out to be wrong." Which principle should have caught this?
12. "The agent explained what it *would* do but didn't actually do it." Which principle fixes this?

---

## SOURCES

This lesson is grounded entirely in:
- **Agent Factory System of Record** — "Problem Solving with General Agents: A 90-Minute Crash Course" (problem-solving-crash-course)
- All principles, examples, and failure modes come directly from AF

Referenced concepts:
- The Five Powers of Code (precise thinking, orchestration, memory, compatibility, tool creation)
- Mode 1 vs. Mode 2 distinction
- The "25 minutes vs. 90 minutes" case (Student A vs. Student B)
- The Lindy Effect (old tools survive because they work)

---

**Good luck on your test this Saturday. You've got this.** 🎓

**The gap between a wandering agent and a landing agent is not intelligence. It's discipline. Master the Seven Principles and you will master how to work with agents.**

---

## QUICK REFERENCE: Print This

### The Seven Principles Cheat Sheet

```
P1: BASH = ACTION, NOT TALK
    "Brief the hands, not the brain"
    
P2: CODE = UNIVERSAL INTERFACE  
    "Define the structure you want"
    
P3: VERIFY = CORE STEP
    "Quote the source for each claim"
    
P4: DECOMPOSE = SMALL REVERSIBLE STEPS
    "Finish, check, save, continue"
    
P5: FILES = MEMORY
    "What matters lives in a file"
    
P6: CONSTRAINTS = GUARDRAILS
    "Start restrictive, expand as needed"
    
P7: OBSERVABILITY = TRANSPARENCY
    "See what it actually did"
```

### The Workflow Pattern

```
INSTRUCTION:
"Read X. Extract Y. Verify against Z. 
Save to FILE. Stop and wait for OK before next step."

RESULT:
Input → Action → Artifact → Verification → File → Next step
```

### Common Mistakes

❌ Asking questions instead of giving instructions  
❌ Vague prose instead of structured format  
❌ Trusting output without verification  
❌ One big change instead of small steps  
❌ Forgetting to save progress between sessions  
❌ Giving too much access without constraints  
❌ No way to see what the agent actually did  

✅ Give instructions with clear input/output  
✅ Define the structure (table, template, checklist)  
✅ Check every claim against sources  
✅ Small reversible steps, verified each time  
✅ Save to files after each step  
✅ Minimal permissions, ask before actions  
✅ Full transparency on commands/changes
