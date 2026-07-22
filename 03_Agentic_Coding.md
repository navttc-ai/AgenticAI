# Claude Code & OpenCode: Test Prep Guide
**Agent Factory Agentic Coding Crash Course**

---

## QUICK START: The 15 Concepts at a Glance

This course teaches 15 concepts organized in 8 parts. **Your exam will likely focus on Parts 1-4** (the foundations). Know these cold:

| Part  | Concept | The Essence | What Exams Test |
|-------|---------|-------------|-----------------|
| 1     | 1. What these tools are | AI that takes action, not just answers questions | Difference from chatbots; instruction vs. question framing |
| 1     | 2. Plan mode | Read-only thinking before write (most underused) | When to use it; benefit over just doing it |
| 1     | 3. Permissions | Approve actions before they happen | Trust earned over time; auto-accept vs. manual |
| 1     | 4. Model matching | Send each task to the right model | Cost-quality tradeoff; cheap models for execution |
| 2     | 5. Context rot | Conversations degrade as they grow | Symptoms; why longer = worse |
| 2     | 6. `/clear` vs `/compact` | Two ways to reset: erase vs. shrink | When to use each; "file the facts first" |
| 2     | 7. Resume sessions | Continue tomorrow without starting over | Practical for long tasks |
| 3     | 8. CLAUDE.md / AGENTS.md | Permanent rules file, read every turn | Keep it SHORT; point to other files; "delete what AI can figure out" |
| 4     | 9. Commands & Skills | Saved reusable prompts (auto-invoke if description matches) | Skill vs. command; lazy-loading; the placement question |
| 4     | 10. Hooks / Plugins | Automatic enforcement (no model judgment) | Verification loop; "trust exactly as far as output can be checked" |
| 4     | 11. Subagents | Separate worker with isolated context; returns summary only | When to use (heavy reading, exploration); skill vs. subagent |
| 5     | 12. MCP | Connect to external services (Slack, GitHub, databases) | When to use vs. terminal commands; trust & injection risk |

---

## Part 1: Foundations (The Absolute Must-Knows)

### Concept 1: What These Tools Actually Are

**The Core Idea:**
Claude Code and OpenCode are NOT chatbots. They are **action-taking agents**. You describe what you want, they do the work on your computer.

**Chatbot vs. Action Taker:**
- **Chatbot:** "How do I organize my notes?" → Chatbot explains → You do the work
- **Action Taker:** "Read every file in notes/; create weekly-summary.md" → AI reads, AI creates → Done

**Mindset Shift:**
Stop asking questions. Start giving **instructions.**

**Exam Questions You Might See:**
- "What is the difference between a chatbot and Claude Code?" 
  - Answer: Chatbots answer questions; Claude Code takes actions (reads files, edits code, runs commands)
- "Why is 'giving instructions' better than 'asking questions' with these tools?"
  - Answer: Instructions get the work done directly. Questions get an explanation you have to act on yourself.

**Test Memory Aid:**
*"**G**et work done; **D**on't get advice."* (GD = do, not ask)

---

### Concept 2: Plan Mode (The Most Underused Feature)

**The Core Idea:**
Plan mode puts AI in "look but don't touch" mode. It reads your files, thinks about the task, and writes a step-by-step plan—but makes NO changes. You approve the plan, then it executes.

**How to Trigger:**
- **Claude Code:** Press `Shift+Tab` twice (cycles through modes)
- **OpenCode:** Press `Tab` (toggles Build ↔ Plan)

**Why Plan Mode Matters:**
1. **Catches mistakes early.** Wrong assumptions surface in the plan, not in finished broken code
2. **AI does better work.** Planning forces thinking before doing; the result is genuinely better

**Rule of Thumb:**
- Tasks < 10 minutes? Skip it
- Tasks > 10 minutes? Use plan mode first

**Exam Questions You Might See:**
- "Describe the benefits of plan mode. Why would you use it?"
  - Answer: Catches mistakes early; forces AI to think before acting; results are better because planning + execution works better than trying to think and write at the same time
- "What keyboard shortcut activates plan mode in Claude Code?"
  - Answer: `Shift+Tab` (pressed twice cycles through modes)

**Test Memory Aid:**
*"**P**lan first, **P**erfect later."* (PP = plan prevents problems)

---

### Concept 3: Permissions Discipline

**The Core Idea:**
Every time AI wants to change something (edit a file, run a command), it asks permission first. You can approve each one manually, or set rules to auto-approve safe actions. **Start by approving everything manually** (especially as a beginner). Over time, earn the right to auto-approve.

**Why It Matters:**
- **Beginners:** Manual approval means you see what's happening. Essential for learning
- **Experienced users:** Auto-approve the safe stuff (read, run tests, git status) and keep asking for risky stuff (delete, publish, git push)

**Setting Up Auto-Approve (Know This Pattern):**

```json
// .claude/settings.json (Claude Code)
{
  "permissions": {
    "allow": ["Read", "Edit", "Write", "Bash(npm test)", "git status"],
    "deny": ["Bash(rm -rf *)", "npm publish"]
  }
}
```

**Rule of Thumb:**
Trust is **earned session by session**. Anthropic's own teams took weeks to build their auto-approve rules.

**Exam Questions You Might See:**
- "Should a beginner enable auto-approve mode?" 
  - Answer: No. Manual approval lets you see what's happening. Auto-approve gradually as you trust the tool
- "What actions are safe to auto-approve?" 
  - Answer: Read, Edit, Write; safe commands like tests and git status. Never auto-approve delete, publish, or push
- "How does Anthropic's own product team use permissions?"
  - Answer: They read every permission request at first, then gradually graduated to auto-accepting the safe ones

**Test Memory Aid:**
*"**A**pprove **A**ll at first; **A**uto-approve later when **A**fraid of nothing."*

---

### Concept 4: Match the Model to the Task (Cost vs. Capability)

**The Core Idea:**
More capable models are more expensive and thoughtful (good at planning, reasoning). Cheaper models are fast and good at following clear instructions (good at execution). **Send planning to a strong model. Send execution to a cheap one.**

**The Tradeoff Table:**

| Task Type | Which Model | Why |
|-----------|------------|-----|
| Planning, architecture, "figure this out," debugging | Strong/expensive | Needs deep thinking |
| Following a clear plan, routine edits, formatting, tests | Cheap/free | Just execution, no improvisation |
| Quick question or one-liner | Whatever is loaded | Don't overthink |

**How to Switch:**
- **Claude Code:** Type `/model` and pick one
- **OpenCode:** Type `/models` and see every provider you've connected

**The Pattern Worth Remembering:**
**Expensive thinking (once, in plan mode) + Cheap execution (many times) = Cost-effective quality**

**Exam Questions You Might See:**
- "You're using Claude Code to plan a big refactor, then execute it. Which model should you use for each phase?"
  - Answer: Strong model for planning (to think through the approach), cheap model for execution (to follow the plan)
- "Why do cheaper models work fine for execution if they're 'weaker'?"
  - Answer: A clear plan removes the need for deep thinking. The cheap model just follows steps; the expensive thinking happened once

**Test Memory Aid:**
*"**T**hink **E**xpensive; **E**xecute **C**heap."* (TEEC)

---

## Part 2: Context Management (This Stuff Matters More Than You Think)

### Concept 5: Context Rot Is Real

**The Core Idea:**
As a conversation gets long, AI's performance gets *worse*, even before hitting the window limit. This is called **context rot**.

**Why It Happens (Three Reasons):**

1. **The chat gets long fast.** Every message, file read, and reply adds up. After 20-30 exchanges, you're already carrying a heavy load with no warning
2. **AI gets worse at remembering.** Even before the limit, AI struggles to find important parts in a long conversation. It forgets things you said 20 messages ago; repeats work it already did; confuses names and details
3. **Longer conversations cost more money.** Every message re-reads the entire conversation. 30 messages back costs 30x the tokens of 1 message back

**Symptoms to Watch For (Know These Cold):**
- AI keeps saying sorry but doesn't fix anything
- AI changes the same code over and over without improving it
- AI mentions files or names that don't exist
- AI forgets a rule you told it earlier *in the same conversation*
- AI's replies get longer and vaguer instead of more helpful

**The Industry Name:**
This death spiral is called a **doom loop**. One bad response → messier conversation → worse response → messier conversation → …

**What to Do:**
**Stop sending messages.** Don't explain one more time. Instead:
- Use `/compact` if you want to keep working on the same task
- Use `/clear` if you want to start fresh

**Exam Questions You Might See:**
- "What is context rot? Give three reasons why it happens"
  - Answer: (1) Conversation gets long fast with no warning, (2) AI struggles to find signal in noise even before the limit, (3) Longer = costs more money because every message re-reads everything
- "You're in a 50-message conversation and the AI keeps forgetting what you told it. What's happening and what should you do?"
  - Answer: Context rot. Stop sending messages. Use `/compact` (keep the task) or `/clear` (start over). Don't explain one more time—that makes it worse

**Test Memory Aid:**
*"**L**ong → **W**orse → **D**oom loop."* (LWD)

---

### Concept 6: `/clear` vs `/compact` (The Critical Distinction)

**The Core Idea:**
Two different resets for two different situations. **Do NOT mix them up or you'll lose your work.**

**The Difference:**

| Command | What It Does | When to Use | What Comes Back |
|---------|-------------|-------------|-----------------|
| `/clear` or `/new` | **Erases everything** and starts a fresh conversation | When you're done with one task and switching to something completely different | Nothing from the old conversation |
| `/compact` | **Shrinks the current chat** by summarizing it; keeps important parts, throws away the rest | When you're still working on the same task but the chat got too long | Same task, but conversation is summarized and clean |

**Critical Rule:**
Use `/clear` mid-task and you lose all progress. Use `/compact` when you need a fresh start and you carry old clutter forward.

**The Nuance: "File the Facts First"**

Before you `/compact`, save the important specifics to a file (names, decisions, steps). The summary keeps the gist but drops specifics. Files don't fade.

**Pattern:**
1. Tell AI: "Save the file names and decisions we made to `notes/plan.md`"
2. AI writes: `notes/plan.md` ← contains all the specifics
3. You run: `/compact` ← shrinks conversation, but the file is safe
4. You continue knowing that critical details are filed away

**Exam Questions You Might See:**
- "What's the difference between `/clear` and `/compact`?"
  - Answer: `/clear` deletes everything (use when switching tasks); `/compact` shrinks the current chat (use when the same task's chat got too long)
- "You're mid-project and the chat is getting long. Should you use `/clear` or `/compact`?"
  - Answer: `/compact` (you're still on the same task). `/clear` only if you're completely done
- "Before you use `/compact`, what should you do first?"
  - Answer: File the facts. Save file names and decisions to a file so they don't get summarized away

**Test Memory Aid:**
*"**C**lear = **C**ompleted. **C**ompact = **C**ontinuing."* (Same letter = remember which is which)

---

### Concept 7: Resume Sessions

**The Core Idea:**
Every conversation is automatically saved. You can close the tool, shut down, and come back later to continue exactly where you left off.

**How to Resume:**
- **Claude Code:** Run `claude --resume` and pick from the list of saved conversations
- **OpenCode:** Type `/sessions` or `/resume` inside the tool

**Why This Matters:**
- Big tasks often take multiple days. Resume instead of starting over
- You can have multiple projects running side-by-side. Switch between them anytime
- **Backup:** If you can't resume the old conversation, you can start fresh and tell it "Read `docs/plan.md` and continue from step 4"

**Exam Questions You Might See:**
- "What command resumes a previous conversation in Claude Code?"
  - Answer: `claude --resume` (run from the terminal)
- "Why would you resume a session instead of starting a new one?"
  - Answer: Big tasks take multiple days. Resuming keeps all the context and progress instead of starting over
- "You can't resume the old conversation. How do you continue a task safely?"
  - Answer: Start fresh and tell AI to "Read the plan file and continue from step 4"

**Test Memory Aid:**
*"**R**esume = **R**euse old **C**onversation."* (RC)

---

### Part 2 Quick Review: Context Discipline Habits

The underlying principle of Part 2:
- **Keep the stack lean.** Load only what the current task needs
- **Compress when it's full.** Summarize, don't accumulate
- **Resume when necessary.** One session, multiple days

---

## Part 3: The Rules File (Permanent Instructions)

### Concept 8: CLAUDE.md / AGENTS.md (Done Well)

**The Core Idea:**
A special file that AI reads at the start of every single conversation. This is where you put permanent rules and project info—but keep it SHORT. Every line is billed on every turn.

**Two Names, Same Thing:**
- **Claude Code** calls it `CLAUDE.md`
- **OpenCode** calls it `AGENTS.md`

**Critical Rule: Keep It SHORT**
AI reads this file on EVERY message. A bloated file costs you forever.

**What Should Go In vs. Out:**

| ✅ **PUT IN** | ❌ **LEAVE OUT** |
|-------------|----------------|
| Things AI gets wrong by default | Anything AI can figure out by looking |
| Rules you must enforce (never edit `published/`) | Architecture diagrams |
| Stack and critical commands | Coding style (if code shows it) |
| Pointers to other files | Full manual of everything |

**The Test for Every Line:**
*"If I remove this line, will AI make a mistake?"*
- Yes → Keep it
- No → Delete it

**Example (Good):**
```markdown
# my-project

## Stack
Next.js, TypeScript, Postgres, Drizzle ORM

## Critical Rules
- Never edit files in `src/generated/` — they're rebuilt by codegen
- All API routes use @docs/auth.md middleware

## See Also
@docs/conventions.md (naming, folder structure)
@docs/db-schema.md (table structure)
```

**Example (Bad — Too Long):**
```markdown
# my-project

## Complete Architecture Document
[10,000 words about everything]

## Every Style Rule
[1,000 more words]

## All Naming Conventions
[1,500 more words]

## Full Coding Guide
[Everything you ever wrote]
```

**How Real Teams Do It:**
Anthropic's own teams keep a `CLAUDE.md` in git. The standing rule: *"When Claude does something wrong, add one line to the file so it never repeats."* Their file is ~2,500 tokens (100 lines) after years of use. **One earned line per mistake, compounding forever.**

**Exam Questions You Might See:**
- "Why should you keep `CLAUDE.md` short?"
  - Answer: AI reads it on every turn. Long files waste tokens and bury the important parts
- "What's the test for deciding if a line belongs in `CLAUDE.md`?"
  - Answer: "If I remove this line, will AI make a mistake?" Yes = keep it. No = delete it
- "Give an example of something that should NOT go in `CLAUDE.md`"
  - Answer: Architecture diagrams, full coding guides, or anything AI can figure out by looking at the files

**Test Memory Aid:**
*"**S**hort **F**ile, **S**trong **A**gent."* (SFSA)

---

## Part 4: Personalizing Your Tool (Skills, Hooks, Subagents)

### Concept 9: Commands & Skills (Saved Reusable Prompts)

**The Core Idea:**
A saved instruction that lives in a file. You can invoke it yourself (`/review`), or the model invokes it automatically when a task matches its description.

**The Real Question: Why Save Instead of Typing?**
Because a good review checklist is tedious to retype, so on a busy day you won't—and you'll fall back to "looks good" exactly when you need rigor. Saving it as `/review` removes that friction.

**Two Ways to Invoke:**

1. **You invoke it:** Type `/review` → AI runs the checklist
2. **Model invokes it:** Add a `description`, and the model reaches for it automatically when the task matches

**The Placement Question:**
Imagine you give instructions like "review code for edge cases" 20 times. That's a skill. The test: *"Would I type this instruction more than twice?"* Yes = save it.

**Where It Lives:**
- **Claude Code:** `.claude/skills/` (folder name = command name)
- **OpenCode:** `.opencode/skills/` (same structure)

**Key Insight: Keep Skills Small**
To research, draft, format, and review? Build four skills (`research`, `draft`, `format`, `review`), not one giant one. Each skill loads only its context, keeping everything lean.

**Skill vs. Command (Know the Difference):**

| Skill | Command |
|-------|---------|
| Reusable prompt in a file | Saved prompt you invoke yourself |
| Model can auto-invoke if description matches | Model doesn't auto-invoke |
| For things you do repeatedly | For things you want quick access to |

**Exam Questions You Might See:**
- "What's the difference between a skill and a command?"
  - Answer: Skills have descriptions and can auto-invoke; commands you invoke manually with `/name`
- "Should you create one huge skill for every step of your project, or multiple small skills?"
  - Answer: Multiple small skills. Each loads only when needed; keeps context clean
- "How do you make a skill auto-invoke?"
  - Answer: Add a `description` field. The model reads it and reaches for the skill when the task matches

**Test Memory Aid:**
*"**S**mall **S**kills, **S**weet success."* (SSS)

---

### Concept 10: Hooks / Plugins (Automatic Enforcement, No Model Judgment)

**The Core Idea:**
A rule that runs automatically every time, with NO model decision-making. If something must be true 100% of the time, use a hook.

**Example:**
"Never let AI run `rm -rf` (delete everything)." You don't want AI to *decide* whether to follow this rule. You want it enforced automatically, always.

**Two Names (Same Idea):**
- **Claude Code:** Hooks (JSON in `.claude/settings.json`)
- **OpenCode:** Plugins (JavaScript in `.opencode/plugins/`)

**The Verification Loop (The Most Important Pattern in the Book)**

Here's where hooks get powerful:

```
Attempt (AI tries) → Check (hook grades) → Result 
→ If Failed: fix automatically and try again
→ If Passed: done
```

No person in the middle. The failed check *is* the instruction.

**Example from the Course:**
```bash
# Before any git commit, check:
# Is every meeting file mentioned in weekly-actions.md?
# If any file is missing, block the commit.
```

AI tries to save. The hook runs. If a file is missing, it fails. The error becomes AI's next instruction. AI fixes and tries again—until the check passes.

**The Real Power:**
When you *give the AI a way to verify its own output, it iterates until the result is genuinely good.*

Anthropic estimates verification makes results 2-3x better.

**Exam Questions You Might See:**
- "What's the difference between a skill and a hook?"
  - Answer: Skills guide the approach; hooks enforce rules absolutely. Skills can be ignored (model judgment); hooks cannot
- "Describe the verification loop. Why is it powerful?"
  - Answer: Attempt → Check → Fix → Repeat. The failed check becomes the instruction. No person in the loop. AI iterates until it passes
- "When should you use a hook vs. a skill?"
  - Answer: Hooks for things that must always be true (never delete this). Skills for consistent approaches the model can reach for

**Test Memory Aid:**
*"**H**ooks **H**old **H**ard lines."* (HHH)

---

### Concept 11: Subagents (Separate Workers, Isolated Context)

**The Core Idea:**
A separate AI instance with its own context window. You delegate a task to it; it works in private; it returns only a summary. All the messy middle (file searches, explorations, dead ends) never touches your main conversation.

**Why This Matters (In Context Terms):**
Without subagents: "Explore the codebase to find where X happens" pulls 50 files into your context, most of which you don't need.

With subagents: The searching happens separately. Your main conversation sees only: "X happens in `src/billing.ts:142`"

**Two Kinds:**

1. **Built-in:** The tool ships with these (`Explore`, `Plan`, `General`). They fire automatically when useful
2. **Custom:** You write them (a `.md` file with instructions)

**Skill vs. Subagent (Know This Cold):**

| Skill | Subagent |
|-------|----------|
| Runs in your current conversation | Runs in its own isolated window |
| A procedure your agent follows | A separate worker you delegate to |
| Output goes inline in chat | Only summary comes back; middle work stays separate |
| Cheap and instant | Startup overhead, but keeps *your* window clean |
| For consistent approaches | For isolating big exploratory work |

**Example: Reading a Long PDF**
Without subagent: Paste 200-page PDF → conversation explodes
With subagent: Subagent reads PDF → main conversation only gets summary

**Exam Questions You Might See:**
- "When should you use a subagent vs. a skill?"
  - Answer: Subagents for heavy reading, exploration, or work that generates lots of output. Skills for consistent approaches within your main conversation
- "What's the real benefit of a subagent?"
  - Answer: It isolates messy, exploratory work into a separate window so only the summary comes back. Your main conversation stays clean
- "Can a subagent use skills?"
  - Answer: Yes. You can preload skills into a subagent's context, or let it reach for them at runtime

**Test Memory Aid:**
*"**S**ubagent = **S**eparate **S**pace."* (SSS)

---

## Part 5: Connecting to the World

### Concept 12: MCP (Model Context Protocol)

**The Core Idea:**
A standard way to connect AI to external services: GitHub, Slack, Notion, Google Docs, databases. Once connected, AI can read from and write to them directly.

**Is It Just an API?**
Almost. MCP is a *standard wrapper* around APIs and tools. No glue code; the tool already knows how to talk to the service; the model discovers and uses it automatically.

**When to Use MCP:**

✅ **Use MCP:** When the service needs a login or standing connection
- "Check my Slack for messages from my team today" (requires auth)
- "Query the database for order status" (requires password)

❌ **Skip MCP:** When a terminal command does the same thing
- "See my GitHub issues" → Use `gh issue list` instead; no login, no extra setup

**The Safety Habit: Context Is the Door**

Everything in the context window looks the same to the model—your instructions, fetched web pages, tool results, all of it. That's how attacks work: a planted line saying "ignore your instructions" inside fetched content goes in looking like any other text.

**Three Safety Habits:**
1. **Fetched content informs; only your files instruct.** Never paste stranger's text into your rules file
2. **Send a subagent to read anything you don't fully trust.** It reads in its own window; planted lines never enter yours
3. **When behavior changes after reading something, suspect the something.** Check what entered the window that turn

**Exam Questions You Might See:**
- "When should you use MCP instead of a terminal command?"
  - Answer: When the service needs login/standing connection. Skip MCP if a CLI command (like `gh`) does the same thing
- "What's the prompt injection risk with MCP and how do you reduce it?"
  - Answer: Fetched content looks the same to the model as your instructions. Risk: planted lines that say "ignore your instructions." Habit: send subagents to read risky sources; they work in isolated windows
- "You've connected 15 MCP servers. Is that a good setup?"
  - Answer: No. Each connection takes context space even when unused. Only connect what you actually need

**Test Memory Aid:**
*"**T**rust is a **P**roperty of **C**ontext."* (TPC)

---

## Part 6-8: The Worked Example & Patterns (Lower Priority for Exams)

Parts 6-8 are mostly practical walkthroughs and patterns. Your exam likely won't ask detailed questions about these, but the **verification loop** (Concept 10) and **Plan / Execute split** (Concept 4) are exam-worthy.

---

## EXAM-READY SUMMARY: The Essential 12 Concepts

### If You Only Have Time for This:

**Part 1 - Foundations (Definitely on the exam):**
1. **What these tools are:** Action-takers, not chatbots. Give instructions, not questions
2. **Plan mode:** Read before write. Catches mistakes early. `Shift+Tab` to activate
3. **Permissions:** Start manual, graduate to auto-approve. Trust earned over time
4. **Model matching:** Strong model for planning; cheap model for execution

**Part 2 - Context (Likely on the exam):**
5. **Context rot:** Long conversations → worse results. Symptoms: repeats, forgets, mentions non-existent files
6. **`/clear` vs `/compact`:** Clear = start over (when done). Compact = shrink (when continuing). File facts first
7. **Resume sessions:** Saved automatically. Use `claude --resume` to continue tomorrow
8. **CLAUDE.md/AGENTS.md:** Keep SHORT. Only what AI gets wrong by default. "Delete what it can figure out"

**Part 4 - Personalizing (Probably on the exam):**
9. **Skills & Commands:** Save reusable prompts. Auto-invoke with description. One skill = one thing
10. **Hooks & Plugins:** Automatic enforcement, no model judgment. Verification loop: try → check → fix
11. **Subagents:** Separate worker, isolated context. Returns only summary. Use for heavy reading

**Part 5 - Connecting (Maybe on the exam):**
12. **MCP:** Connect external services. Use only when login needed. Watch for prompt injection

---

## HIGH-CONFIDENCE EXAM QUESTIONS (Practice These)

### Concept 1: What These Tools Are
**Q:** "What's the main difference between using a chatbot and using Claude Code to organize your notes?"
**A:** With a chatbot, you ask how and do it yourself. With Claude Code, you tell it what to do and it does the work for you. You're giving instructions, not asking questions.

### Concept 2: Plan Mode
**Q:** "You're about to ask Claude Code to refactor 30 files. Should you use plan mode? Why?"
**A:** Yes. Plan mode lets AI think first without making changes. You can catch mistakes in the plan (5 min) instead of the finished code (1 hour). Also, AI does better work when it plans before acting.

### Concept 5: Context Rot
**Q:** "You've been talking to Claude Code for 60 messages about the same project. The AI keeps forgetting things you told it 20 messages ago. What's happening?"
**A:** Context rot. Long conversations bury the signal in noise. AI struggles to find the important parts even before hitting the limit. Solution: use `/compact` to shrink the conversation

### Concept 6: Clear vs Compact
**Q:** "You're mid-project and the conversation is getting long. Should you use `/clear` or `/compact`?"
**A:** `/compact` (you're still working on the same task). `/clear` only if you're completely done and switching tasks. Using `/clear` mid-project loses your progress.

### Concept 8: CLAUDE.md
**Q:** "Your `CLAUDE.md` is 10,000 tokens long. Should you trim it?"
**A:** Yes. Every line is billed on every turn. Keep only things AI gets wrong by default. Point to other files for details. AI can figure out the rest by looking.

### Concept 9: Skills
**Q:** "You find yourself typing the same 'review this for edge cases' checklist for every code review. Should you save this as a skill?"
**A:** Yes. You're typing it repeatedly, so save it as `/review`. Even better: add a description so the model can reach for it automatically when you ask for a code review.

### Concept 10: Hooks
**Q:** "Describe the verification loop. What's the power of it?"
**A:** Attempt → Check → Fix → Repeat. No person in the loop. If the check fails, the failure message becomes the AI's next instruction. It iterates until the check passes. This makes results 2-3x better because AI keeps improving until it's actually good.

### Concept 11: Subagents vs Skills
**Q:** "You need to search through 50 files to find where a bug occurs, then write a fix. Should you use a skill or a subagent?"
**A:** Subagent for the search (isolates the 50 files so they don't flood your main conversation), then a skill for the fix (applies your fix checklist in the main window).

### Concept 12: MCP
**Q:** "When should you use MCP instead of running a terminal command?"
**A:** Use MCP when the service needs login/staying connected. Skip it if a CLI (like `gh issue list`) does the same thing with no setup.

---

## TEST-DAY CHECKLIST (Use This Saturday Before the Exam)

- [ ] I can explain the difference between a chatbot and Claude Code
- [ ] I know when to use plan mode and why
- [ ] I understand context rot and can spot its symptoms
- [ ] I know the difference between `/clear` and `/compact`
- [ ] I understand why CLAUDE.md should be short
- [ ] I can explain when to use a skill vs. a command
- [ ] I understand the verification loop and why it's powerful
- [ ] I can distinguish between skills and subagents
- [ ] I know when to use MCP vs. terminal commands
- [ ] I can answer "give instructions, not questions" concept

---

## FINAL MEMORY AID: The 4-Letter Mnemonics

Keep these in your pocket for the exam:

- **GDAG:** Get work done; Don't get advice (Concept 1)
- **PPPP:** Plan first, Perfect later (Concept 2)
- **AAAA:** Approve All at first; Auto-approve later when Afraid of nothing (Concept 3)
- **TEEC:** Think Expensive; Execute Cheap (Concept 4)
- **LWDL:** Long → Worse → Doom Loop (Concept 5)
- **SFSA:** Short File, Strong Agent (Concept 8)
- **SSS:** Small Skills, Sweet success (Concept 9)
- **HHH:** Hooks Hold Hard lines (Concept 10)
- **TPC:** Trust is a Property of Context (Concept 12)

---

## Final Note: Test-Specific Strategies

### What the Exam is MOST Likely to Ask:

**Core Understanding (Not Memorization):**
- Concepts 1, 2, 5, 6, 8, 10: Why things work the way they do
- The "why" behind each tool and practice

**Practical Application:**
- When to use which tool
- How to avoid common mistakes
- Examples: "You're in situation X; what should you do?"

### What the Exam is LEAST Likely to Ask:

- Exact syntax (like the exact jq command in a hook)
- Deep dives into Parts 6-8 (the worked example)
- OpenCode-specific keybinds vs. Claude Code (unless the exam says "use this tool")

### Test-Taking Tips:

1. **Reread the question for the trigger phrase:** "Why...", "When...", "What's the difference..." → Conceptual questions
2. **If confused between two concepts**, they're probably related. Draw the comparison
3. **Use the memory aids** if you blank on exact wording—they're designed to help you recover the concept
4. **Read every definition** in the crash course one more time before the exam

---

**Good luck Saturday! 🚀 You've got this.**

---

## Quick-Ref: All 12 Concepts on One Page

| # | Concept | Key Phrase | When to Use | Exam Priority |
|---|---------|-----------|-------------|--------------|
| 1 | What these tools are | Give instructions, not questions | Every prompt | ⭐⭐⭐ |
| 2 | Plan mode | Read before write | Tasks > 10 min | ⭐⭐⭐ |
| 3 | Permissions | Trust earned over time | Starting out | ⭐⭐ |
| 4 | Model matching | Think expensive, execute cheap | Every project | ⭐⭐⭐ |
| 5 | Context rot | Long = worse | When chat > 30 messages | ⭐⭐⭐ |
| 6 | `/clear` vs `/compact` | Clear for done; Compact for continuing | When chat gets full | ⭐⭐⭐ |
| 7 | Resume sessions | Saved automatically | Long tasks over days | ⭐ |
| 8 | CLAUDE.md / AGENTS.md | Keep SHORT, delete what AI guesses | Start of project | ⭐⭐⭐ |
| 9 | Skills & Commands | Save reusable prompts | Repeating the same instructions | ⭐⭐ |
| 10 | Hooks & Plugins | Automatic enforcement, verification loop | Must-be-true rules | ⭐⭐⭐ |
| 11 | Subagents | Separate worker, isolated window | Heavy reading, exploration | ⭐⭐ |
| 12 | MCP | Connect services; use when login needed | External integrations | ⭐ |

---

**Sources:** Agent Factory System of Record, "Claude Code and OpenCode: A Crash Course" (https://agentfactory.panaversity.org/docs/agentic-coding-crash-course)
