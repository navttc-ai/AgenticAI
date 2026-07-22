# OpenClaw with General Agents: The Complete Lesson
## Master the Personal AI Employee in 60 Minutes

---

## **Opening Hook: The Delegation Problem**

Imagine this: your inbox has 47 messages. Some are simple ("Can you ship today?"). Some need research ("What does our competitor charge for this?"). Some are complex decisions ("Should I approve this contract?"). You spend three hours a day on triage that only _you_ can decide on, which means the emails you could delegate, you don't—because explaining the task to someone else takes longer than doing it yourself.

But what if you had a co-worker who:
- Reads all 47 messages instantly
- Handles the ones you don't need to see
- Researches the ones that need research  
- Escalates only the ones that need your decision
- Remembers what you care about without you repeating it

And never takes a lunch break, never forgets, never has a bad day.

That's not science fiction in 2026. That's OpenClaw. By the end of this lesson, you'll understand exactly how it works, what makes it different from a chatbot, and how a general agent (like Claude Code) can set it up for you without you touching the command line.

---

## **Core Concept: What Is OpenClaw, Really?**

### In Plain English

OpenClaw is a Personal AI Employee: a program that runs on your laptop, replies through messaging apps you already use (WhatsApp, Telegram, Discord), remembers context about you, and can actually do tasks like research, read files, or send calendar invites. Unlike ChatGPT (which you visit), OpenClaw is always-on and always-yours.

### Real-World Analogy

Think of hiring a research assistant you trust. On day one, they don't know anything about you: your priorities, your writing style, your industry terminology, which tasks matter. Over a week, they learn your voice, your processes, your values. That's OpenClaw. It starts generic; through Scenarios 1-4, you teach it to be _your_ assistant, not anyone's.

### The Technical Definition

From Agent Factory: OpenClaw is an open-source framework that runs an LLM-based agent in a persistent session on your machine. It binds to messaging channels (WhatsApp, Telegram, etc.) through adapters, maintains a durable workspace at `~/.openclaw/workspace/`, and executes tool calls in a controlled loop where the agent decides what to do next based on what you asked and what it knows about you. The session runs as a background service, so the agent is always ready—no startup time, no context refetch. You message it; it replies.

### Why This Matters

The key insight: **agents are different from chatbots**. A chatbot responds to your input. An agent _decides what to do_. It reads your message, checks what tools it has (web fetch, calendar access, file read), decides which tools to use, calls them, sees the results, and builds an answer. Before you've watched an agent do this once, "agent" sounds like marketing. After you've watched it once—seeing the log scroll with a message arriving → the model call → a tool invocation → the result → an outbound reply—you understand you're not talking to a language model anymore. You're delegating to a decision-maker.

### One Concrete Example

You're on vacation and send from your phone: "Has anyone urgent tried to reach me?"

A chatbot responds: "I don't have access to your email."

An agent:
1. Receives your message
2. Thinks: "I need to check email for urgent messages. Let me fetch recent unread mail."
3. Calls the email tool
4. Reads the results
5. Thinks: "Let me filter for 'urgent' and 'ASAP' flags, scan subject lines."
6. Replies: "One person marked urgent: Sarah about the Q3 budget review. Three others flagged 'ASAP' but they're non-critical."

You didn't explain the steps. The agent _decided_ them. That's the difference.

---

## **Deep Dive: 5 Core Concepts**

### **Concept 1: The Five-Step Collaboration Pattern**

#### The Idea

OpenClaw isn't just you + the AI. It's **three actors**: you, your general agent (Claude Code), and OpenClaw. Each has a job. Understanding where the boundary is between your job and the agent's job explains how the whole system works.

#### How It Works

The rhythm repeats for every task (install, configure, deploy, extend, troubleshoot):

1. **You describe the goal** (one sentence: "I want OpenClaw running and chatting"). Not a script; a brief.
2. **Your agent proposes a plan** (it reads `AGENTS.md`, checks your machine, and says: "Here's what I'll check, what I'll change, where I need you").
3. **You approve and watch** (the agent runs commands, shows you output, applies fixes when things go sideways).
4. **The agent stops at the seam** (some moves only you can do: visiting a website, scanning a QR, pasting your API key). The agent _names_ the seam and waits.
5. **You verify the done-when** (something observable: a reply in the dashboard, a message back from your phone, a file appearing).

#### Practical Example

**Install scenario (Scenario 1):**

- **You:** "Walk me through installing OpenClaw with Gemini free tier before you touch anything."
- **Agent:** [reads AGENTS.md + checks your machine] "I'll install via npm, run the onboard wizard, and get your Gemini key from aistudio.google.com. I need you for: grabbing the free key and pasting it safely."
- **You:** "Go ahead."
- **Agent:** [runs install commands, shows each step, pauses when it needs the key]
- **You:** [visit aistudio.google.com, get key, paste it into the terminal as the agent instructs]
- **Agent:** [finishes install, opens dashboard]
- **You:** [type "hi" in the dashboard, see it reply with "Hi! How can I help?", done-when achieved]

The genius: **the agent does what agents do well (install, check, debug, restart)**. **You do what only you can do (decide, approve, touch your accounts)**. No baby-sitting a terminal, no CLI mystery. Just describe the goal and steer.

#### The Pitfall

Learners often think: "Can't I just run the commands myself?" Yes, you can. But that's like hiring an assistant and then insisting on doing all the work yourself. The point isn't avoiding commands; it's **discovering before you act**. Your agent reads the docs, spots gotchas, applies fixes from the brief before you hit them. You find out why something fails by reading a plain-English explanation, not `npm ERR!` code. The agent is your translator, not your typist.

#### Stop & Think 🤔

- **Question 1:** In the five-step rhythm, why does the agent propose a plan before acting? What problem does that step solve?
- **Question 2:** When the agent says "this is a seam—you need to grab your API key," what does that mean about trust and responsibility? Who's accountable if the key gets stolen?
- **Question 3:** Could the agent handle the seams (the website visit, the QR scan)? Why or why not? What would be lost if it could?

*Suggested reflection time: 2-3 minutes*

---

### **Concept 2: The Agent Loop—How It Actually Decides**

#### The Idea

When you send a message to OpenClaw from your phone, six things happen in order. Watching them scroll past in the log is when you stop thinking "AI" and start thinking "this is a worker." Understanding the loop is the difference between guessing what it'll do and predicting it.

#### How It Works

1. **Inbound message** arrives on your channel (WhatsApp, Telegram, whatever).
2. **Model call #1:** OpenClaw sends your message to Gemini (or Claude, or whatever model) and asks: "What should I do?" The model thinks and says "I need to fetch that person's recent emails" or "I should check their calendar" or "I can answer this directly."
3. **Tool call:** The agent invokes whatever tool the model decided (web fetch, email read, calendar query).
4. **Tool result:** The tool returns data (a web page, five emails, the next three calendar events).
5. **Model call #2:** OpenClaw sends the tool result back to Gemini with a prompt like: "Here's what I found. Give me a one-paragraph summary the user will understand."
6. **Outbound message:** The reply goes back to your phone.

#### Practical Example

**From your phone:** "What did my clients care about most in last week's calls?"

**The loop scrolls:**
1. Message arrives: "What did my clients care about most in last week's calls?"
2. Model thinks: "I should fetch recent call transcripts or notes."
3. Tool call: Agent reads the calendar and meeting-notes folder.
4. Tool result: [returns 15 meeting notes from the past week]
5. Model thinks: "Let me scan for repeated themes, pain points, questions they asked."
6. Model reply: "Across your five client calls, three themes dominated: (1) cost optimization, (2) integration timelines, (3) contract renewal terms. Sarah's team mentioned cost twice. Michael's brought up timelines in all three calls."

The point: **the agent didn't hallucinate**. It didn't guess. It _looked_. It read your actual calendar and notes. That's not luck; that's the loop. Every tool your AI Employee gains (email, files, web, calendar, Slack) just adds more options the model can choose from at step 2. More tools = more decisions it can make without asking you.

#### The Pitfall

Learners often assume: "If it can call tools, can it do anything? Run shell commands? Access any file?" Not if you don't give it permission. The loop is neutral; what tools are **in** the loop is your choice. In Scenario 5, you deliberately add one skill and one tool. The default install has no web access, no email, no file read—so the first few scenarios, the model has only one tool: talking back to you. You expand its toolkit one careful decision at a time.

#### Stop & Think 🤔

- **Question 1:** At step 5 in the loop, why does the model need to see the tool results before writing the reply? Why not just tell the model "use this tool" and skip straight to a reply?
- **Question 2:** If you give your AI Employee web access, what assumption are you making about its judgment? What could go wrong?
- **Question 3:** The loop happens every time you message it—even a one-word "hi." What does that tell you about the computational cost of having an always-on AI employee?

*Suggested reflection time: 2-3 minutes*

---

### **Concept 3: The Workspace—Your Employee's Brain**

#### The Idea

Your AI Employee's behavior doesn't come from magic. It comes from seven markdown files in a folder called `~/.openclaw/workspace/`. Edit one file, restart, and the behavior changes. This is not cloud magic; this is text files on your disk. You own them, you can version control them, you can back them up to GitHub.

#### How It Works

Seven files. Four are personality; three are mechanics:

**Personality files (customize on day one):**
- **`SOUL.md`**: How it talks (direct? hedgy? funny? formal?)
- **`IDENTITY.md`**: Its name and role ("I'm Atlas, your research assistant")
- **`USER.md`**: What it knows about you (your timezone, your priorities, your role)
- **`MEMORY.md`**: Durable facts across channels ("You're preparing a pitch for TechCorp on Wednesday")

**Mechanics files (set once, rarely change):**
- **`AGENTS.md`**: The agent's own operating rules (safety rails, what it can/cannot do)
- **`TOOLS.md`**: Which tools it can use (email? files? calendar? web?)
- **`HEARTBEAT.md`**: Ambient routines (check for urgent emails every 30 minutes, etc.)

Every time your AI Employee reads a message, it loads all seven files into its system prompt. That's context cost: every line in these files is paid for on every reply. So you keep them lean. Two paragraphs per file is plenty.

#### Practical Example

**Default `SOUL.md`** (generic):
> You are a helpful AI assistant. You aim to be friendly and informative. You try to be accurate.

**Custom `SOUL.md`** (yours):
> You are direct and skeptical. You push back on vague requests. You ask clarifying questions before doing research. You prefer one-sentence summaries over long explanations. You assume I'm busy.

Same AI, same loop, completely different personality. Replies change instantly.

#### The Pitfall

Learners often over-design the workspace files. They write a page of context for `USER.md` thinking "more info = smarter decisions." Wrong. Every extra line costs you. A 500-word `USER.md` means every reply to your AI Employee is paying the price. After 100 replies, you've spent $0.50 just on context loading. Keep it lean. Scenario 4 walks you through editing each file once and seeing the behavior change. That's the limit.

#### Stop & Think 🤔

- **Question 1:** Why does every line in `SOUL.md` have a computational cost even for tasks that don't need personality? (Hint: think about the system prompt.)
- **Question 2:** If `MEMORY.md` is supposed to be durable across channels, why doesn't it load automatically? Why does the agent factory design require you to deliberately commit to it?
- **Question 3:** What's in `AGENTS.md` that's different from the one in your zip folder? What purpose does each serve?

*Suggested reflection time: 2-3 minutes*

---

### **Concept 4: Channels—How Your Phone Reaches Your Laptop**

#### The Idea

OpenClaw runs on your laptop. Your phone is physically somewhere else. How does the message get from WhatsApp on your phone to the OpenClaw gateway on your laptop? And why is this called "pairing" instead of just "connecting"?

#### How It Works

**Pairing = trust handshake.** When you pair WhatsApp Business, you scan a QR code from your phone. That QR encodes a symmetric key. Both devices now know the same secret. Messages from your phone get encrypted with that key, sent over the internet to your laptop, and OpenClaw decrypts them because it has the same key. If your laptop is stolen, revoke the device from your phone, and the pairing is dead. No one else can message it.

The three main channels:

- **WhatsApp Business**: Personal number with WhatsApp Business app (not your real account; Meta bans personal accounts for this). Most natural UX. Supports groups.
- **Telegram**: Anyone with your bot's name can message it (unless you lock it down). Instant setup (BotFather gives you a token). Supports groups, threads, file uploads.
- **Discord**: Requires a dedicated bot account. More setup overhead. Good for team access if you want it. Supports server-wide access, private channels, rich embeds.

Each channel is independent. Same AI Employee, three entry points. You can message from WhatsApp in the morning, Telegram at lunch, Discord at night—the AI Employee's memory is shared because they all feed into the same gateway on port 18789.

#### Practical Example

**Scenario 2 walkthrough:**

- You want to message from your phone
- Your agent proposes WhatsApp because it's most natural UX
- Agent asks: do you have WhatsApp Business or only personal? (Personal = friction, skip it, use Telegram instead)
- You have WhatsApp Business installed
- Agent opens a terminal UI with a QR code
- You scan from your phone
- Pairing complete
- You send "hi" from WhatsApp
- It replies back

The agent can't scan the QR (agents can't see phone screens), so it stops at the seam. You scan, tell it "done," and the agent verifies by trying a test message.

#### The Pitfall

Learners often think: "If pairing creates a shared secret, isn't it a security risk?" Only if you leak the key. And the key lives on your laptop in OpenClaw's config, not in chat history or a backupserver. The real risk is: **if you pair multiple people on the same phone, they all message the same AI Employee with the same identity**. If you want your spouse to have a separate channel to the same Employee, you don't pair both on one phone. You add a second channel to OpenClaw (a separate WhatsApp number, a Telegram account, etc.) and pair that. Or you run a second OpenClaw instance for them. Pairing is per-device, not per-person.

#### Stop & Think 🤔

- **Question 1:** Why is pairing called "trust handshake"? What does the shared secret actually protect against?
- **Question 2:** If you revoke a device from WhatsApp, what happens if someone still tries to message your AI Employee from that device's WhatsApp?
- **Question 3:** The lesson says "same AI Employee, three entry points." Could you have three different AI Employees (one personality for WhatsApp, another for Telegram, another for Discord)? How?

*Suggested reflection time: 2-3 minutes*

---

### **Concept 5: The Activation Dance—How Extensions Load**

#### The Idea

When you add a new skill, install an MCP tool, connect a channel, or set up a scheduled task, it doesn't just work on day one. It goes through four states. Learners who understand the dance never panic when a new tool doesn't fire on first try; they know exactly what step is missing.

#### How It Works

Every OpenClaw extension follows the same four-step sequence:

1. **Exists**: The code is installed (a skill folder is copied, an MCP server is registered, a channel adapter is available).
2. **Disabled by default**: Even though it exists, the gateway doesn't use it yet (safety: you don't enable tools automatically).
3. **Enabled**: You flip the switch to turn it on (usually via config or a `/enable` command).
4. **Configured (restart)**: OpenClaw reloads the configuration. The tool is now live.

#### Practical Example

**Installing a skill (Scenario 5a):**

1. **Exists**: Agent runs `npm install [skill-name]`, and the folder lands at `~/.openclaw/skills/[skill-name]/`
2. **Disabled**: OpenClaw sees the folder but doesn't use the skill yet.
3. **Enabled**: Agent edits `openclaw.json` to add `[skill-name]` to the skills array.
4. **Configured**: Agent runs `openclaw gateway restart`. The gateway reloads `openclaw.json`. Next time you message, the skill is available.

If you do steps 1-3 but forget step 4, the skill exists, is enabled, but the gateway still has the old config in RAM. You send a message that should trigger the skill—nothing. You think the install failed. Wrong: you're at step 3.5. One restart, you're done.

#### The Pitfall

Learners often skip the discovery step. "Just tell me what command to run." But the discovery step IS the point. Your agent reads AGENTS.md or the live docs, checks your machine, and says: "Here's what I found, here's what needs to happen, here's where we might get stuck." That's why the agent doesn't just auto-fix; it proposes first. The learning is in the proposal.

#### Stop & Think 🤔

- **Question 1:** Why is "disabled by default" a design choice? What's the security reasoning?
- **Question 2:** When you run `openclaw gateway restart` in step 4, what's actually happening? Why does restarting load the new config?
- **Question 3:** Can you enable a tool without installing it (skip steps 1-2)? What happens?

*Suggested reflection time: 2-3 minutes*

---

## **Systems Thinking: How It All Connects**

Think of OpenClaw like a company with four departments:

```
YOU (Delegator)
    ↓
CHANNELS (Communication layer)
    ├─ WhatsApp, Telegram, Discord (three ways messages get in)
    ↓
GATEWAY (Message processing center, port 18789)
    ├─ Routes messages
    ├─ Manages sessions
    ├─ Dispatches tool calls
    ↓
BRAIN (Workspace files at ~/.openclaw/workspace/)
    ├─ SOUL.md (voice), IDENTITY.md (name), USER.md (context), MEMORY.md (learning)
    ├─ TOOLS.md (what it can do), HEARTBEAT.md (ambient jobs), AGENTS.md (rules)
    ↓
TOOLS & SKILLS (Capabilities)
    ├─ Native: message reply
    ├─ MCP servers: time, email, calendar, web fetch, custom
    ├─ Skills: expertise packs (research, writing, coding, etc.)
    ├─ Scheduled tasks: cron jobs, heartbeats
```

Remove any layer, and the system breaks:

- No **channels** → messages can't arrive
- No **gateway** → messages have nowhere to go
- No **brain** → the AI Employee has no personality or memory
- No **tools** → it can only talk, never do

But add them in order (Scenarios 1-6 do exactly this), and you have a full system by the end.

**The key relationship**: The **loop** (model call → tool call → result → model reply) is the engine. The **workspace** files are the character. The **channels** are the interface. The **gateway** is the orchestrator. A capable **general agent** builds and maintains the whole thing.

---

## **Real-World Application: A Day in the Life**

### The Situation

You're a product manager. You wake up Monday with 60 emails, a packed calendar, and three high-priority customer issues. You have two hours before a board call where you need to pitch Q4 strategy. Manual triage would take an hour. You don't have an hour.

### How It Works (Step by Step)

**7:00 AM** - Your cron job fires (Scenario 6):
- OpenClaw reads your email inbox
- Filters for "urgent" or from VIP customers
- Reads your calendar for the day
- Summarizes: "Two urgent customer issues (contract language, deployment timeline). Three board-prep items need your attention. Fourteen routine emails can wait."
- Sends as WhatsApp message to your phone
- **Outcome**: You've got a 30-second snapshot instead of spending 30 minutes triaging.

**8:15 AM** - First customer issue (Scenario 3, the agent loop):
- You reply from your phone: "What's Sarah's concern about the contract? What's our exposure?"
- Your AI Employee:
  1. Fetches Sarah's emails
  2. Calls the MCP web tool to look up relevant contract terms
  3. Reads your internal policy files (uploaded as skills)
  4. Replies: "Sarah's concerned about data retention clauses. Our standard is 3 years; hers requires 5. This is common in healthcare. Exposure is low; we've negotiated this before."
- **Outcome**: You now have context, not a guess.

**9:30 AM** - Preparing for the board call:
- You: "Help me pitch Q4 strategy. What were last quarter's wins and where did we miss?"
- Your AI Employee:
  1. Fetches your recent meeting notes
  2. Reads your metrics from a connected dashboard
  3. Summarizes: "You delivered feature X on time (wins), missed the launch date for Y (miss), and you're 15% above plan on adoption."
- **Outcome**: Your deck now has data, not just intuition.

**11:45 AM** - Custom reminder fires:
- Your heartbeat (Scenario 6) pings at 11:45 AM: "You have a board call in 15 minutes. Do you need speaker notes?"
- You reply: "Yes, give me a one-pager."
- Your AI Employee synthesizes everything from that morning into a one-pager and sends it.
- **Outcome**: You go into the call prepared, on time, with notes in hand.

**3:00 PM** - Monday debrief:
- You send: "How'd I do on commitments today? Any loose ends?"
- Your AI Employee:
  1. Checks your calendar for what you promised in the board call
  2. Reads your Slack for follow-ups
  3. Scans your email for things you said you'd do
  4. Reports back with a 5-item to-do list
- **Outcome**: You close the day knowing what carries over to tomorrow.

**Throughout the day** - It never forgets:
- Every commitment you make, it learns (Scenario 4, `MEMORY.md`)
- Every pattern (your boss asks about X on Thursdays), it notices
- Every tool you give it (a new MCP server, a new skill), it uses

### Why This Matters to You

The board call was successful not because you're smarter than Monday morning. You're the same person. But your AI Employee **delegated the work of staying organized**. It handled email triage, context gathering, note synthesis, and follow-up tracking. You spent those hours on decisions only you can make.

This is not automation (where a system decides for you). This is **delegation** (where the system decides what you need to know, and you decide what happens next).

---

## **Practical Next Steps: Start Here This Week**

### **Action 1: Run Scenario 1 (Monday, 30 min)**
- **What**: Install OpenClaw, set up Gemini free tier, chat in the dashboard.
- **Why**: Prove the gateway and model are wired.
- **Expected result**: You type "hi" in a browser and get a real reply.

### **Action 2: Run Scenario 2 (Tuesday, 30 min)**
- **What**: Pair WhatsApp or Telegram so you can message from your phone.
- **Why**: Prove the channel works. See the message flow from phone to laptop and back.
- **Expected result**: You send a message from your phone, get a real reply.

### **Action 3: Run Scenario 3 (Wednesday, 20 min)**
- **What**: Send a real task that needs research or tool use. Watch the log.
- **Why**: See the six-line agent loop in real time. Understand that it's not magic; it's a decision-maker.
- **Expected result**: You watch the log scroll and see: message → model call → tool call → result → model call → reply.

### **Action 4: Run Scenario 4 (Thursday, 30 min)**
- **What**: Edit `SOUL.md`, `IDENTITY.md`, `USER.md` to teach it about you.
- **Why**: Prove that it's _your_ employee now, not a generic chatbot.
- **Expected result**: After each edit + `/reset`, the replies are noticeably different. It sounds like your assistant, not OpenAI's.

### **Action 5: Run Scenario 5 (Friday, 45 min)**
- **What**: Install one skill and connect one MCP tool.
- **Why**: Expand its toolkit. Understand the activation dance. See it call the new tool.
- **Expected result**: A skill fires in response to a message that matches its description. The time MCP returns a real timezone conversion.

### **Action 6: Run Scenario 6 (Next Monday, 30 min)**
- **What**: Set up one real cron job (a 7am summary, a Monday morning brief, an end-of-day checklist).
- **Why**: Flip from "I message it" to "it messages me." Prove it can act on its own.
- **Expected result**: Tomorrow morning, at your chosen time, OpenClaw sends you an unsolicited message with real value. Not a demo you disable; something you'll keep running.

---

## **FAQs & Misconceptions**

### **Q: Isn't this just a chatbot with extra steps?**

**A:** No. A chatbot responds to input. An agent _decides what to do_. The first time you watch the log scroll past with message → model thinking → tool call → result → reply, you'll feel the difference. You're not talking to a language model; you're delegating to a decision-maker. The extra steps are the whole point.

### **Q: How is this different from Claude Code or OpenCode?**

**A:** Claude Code is a general agent (you can ask it to do anything). OpenClaw is a specialized agent (it only does the things your Employee's workspace permits). Think of Claude Code as your consultant who you talk to in chat. OpenClaw is your co-worker who's always available on messaging apps and remembers context across channels. Different tool for a different job.

### **Q: Doesn't running this 24/7 cost a fortune?**

**A:** Depends on your model. Gemini free tier is capped (~50 requests/day). Claude via OpenRouter is cheap (~$0.003 per reply). Running a scheduled task that fetches emails every 30 minutes and replies with a summary costs about $0.01/day. The board call scenario, from context gathering to speaker notes, cost roughly $0.05. Nothing is free, but the math is forgiving.

### **Q: What if it breaks? How do I fix it?**

**A:** One recovery prompt works for everything: _"Something didn't work. Read the gateway log, tell me in plain language what you see, and propose a fix I can approve."_ Your agent reads the log, names the problem, and proposes a fix. You approve. That's the entire recovery loop for the whole course. No CLI knowledge needed.

### **Q: Can I run this on my phone instead of my laptop?**

**A:** Not with the current setup. OpenClaw needs a always-on host (your laptop, a Raspberry Pi, a VPS). Your phone can _message_ it, but it can't _run_ it. This is actually a feature: your AI Employee lives where you control it, and your phone is just the remote control.

### **Q: What if I want to share it with my team?**

**A:** Don't. At least not yet. Pairing one person is safe. Pairing a team to the same AI Employee creates ambiguity about who authorized what. The right move is run the full course (Scenarios 1-6), then—**if** you want team access—move to Scenario 8 (NemoClaw sandbox) to lock down what it can do. Then add team members carefully. The brief from Agent Factory has a whole section on multi-agent safety. Don't skip it.

### **Q: This sounds complicated. How long until it's actually useful?**

**A:** Scenario 1 alone (30 min) gets you a chatbot that's better than ChatGPT because it's always-on and on your laptop. Scenarios 1-3 (75 min total) get you an agent that can delegate. Scenarios 1-6 (around 2 hours over a week) get you a tool you'll use daily. It's a ramp. Day one: chatbot. Day two: agent. Day three: your co-worker.

---

## **Summary: The One-Page Takeaway**

### OpenClaw at a Glance

| Concept | Definition | Example |
|---------|-----------|---------|
| **OpenClaw** | Always-on AI Employee running on your laptop, replying via messaging apps | You message WhatsApp at 3am, get a reply with research from your files |
| **Collaboration Pattern** | You describe → Agent proposes → You approve → Agent executes → You verify | "Install OpenClaw." Agent: "Here's my plan." You: "Go ahead." Agent: [does it] |
| **Agent Loop** | Message → Model thinks → Tool call → Result → Model reply → Message back | Message: "Latest deal status?" Loop: [fetch Salesforce] → [read recent notes] → Reply: "Deal closed Tuesday" |
| **Workspace Brain** | Seven markdown files at `~/.openclaw/workspace/` that shape behavior | SOUL.md = personality, IDENTITY.md = name, USER.md = context about you |
| **Channels** | How your phone reaches your laptop (WhatsApp, Telegram, Discord pairing) | Scan a QR from WhatsApp Business, now messages sync to your laptop |
| **Activation Dance** | Four steps every tool goes through: Exists → Disabled → Enabled → Configured (restart) | Install a skill. Enable it. Restart gateway. Now it's live. |
| **Scheduling** | Cron (specific times: "7am daily") or Heartbeat (intervals: "every 30 min") | Every Monday 9am, summarize your week. Every 4 hours, check for urgent emails. |

### Key Relationships

- **Channels** deliver messages to the **Gateway**
- **Gateway** runs the **Agent Loop** using your **Brain** files
- **Brain** files determine personality and memory
- **Tools** (skills, MCP servers) expand what the **Loop** can do
- **Schedules** make it proactive (run without you messaging)

### Test Prep Checklist

- ✅ Can you name the five steps of the collaboration pattern?
- ✅ Can you describe the six-line agent loop (message → model → tool → result → model → reply)?
- ✅ Can you name four of the seven workspace files and what each does?
- ✅ Can you explain why "pairing" creates a shared secret?
- ✅ Can you walk through the activation dance for a new tool?
- ✅ Can you describe one scheduling scenario (cron or heartbeat)?

If you can answer all six, you understand OpenClaw. The test will ask these in different ways (scenario-based, definitions, comparisons), but these six ideas are the foundation.

---

## **Deeper Dive: Optional—For the Curious**

If you want to go deeper before your test:

1. **Read Chapter 56 of Agent Factory** ("Meet Your Personal AI Employee") for the patient, full walkthrough. Seventeen lessons covering voice, multi-agent orchestration, sandboxing, and everything the crash course skips.

2. **Study `AGENTS.md`** (the operational brief from the zip file). It covers principles, safety rails, recovery patterns, the activation dance, and gotchas. Your general agent reads this; you should too.

3. **Understand NemoClaw** (Scenario 8). If your test includes a question about sandboxing or public-facing safety, NemoClaw is the answer. It's an NVIDIA wrapper that runs OpenClaw inside a Linux cage so it can only access one folder and an allow-list of websites.

4. **Explore the Model Context Protocol (MCP)**. Every external tool your AI Employee calls is an MCP server. Understanding how MCP works (your agent calls a tool, the server responds with structured data) is the foundation for adding custom tools.

5. **Connect one real MCP tool yourself** (not just the demo time tool). Try email, calendar, or a custom database. The activation dance is the same for all; seeing it with a real tool is worth the time.

---

## **Final Word: Why This Matters**

OpenClaw with General Agents is not just a technical setup. It's proof that **AI Employees are real**. Not a distant future; today. 

The insight: You don't need to be a coder or a prompt engineer to have an AI Employee. You just need to know:
1. What you want it to do
2. How to teach it about you
3. What tools to trust it with
4. How to verify it's working

That's what the six scenarios teach. That's what this lesson covers. And after your test, when you've moved past the definitions and explanations, that's what you'll actually _use_.

Good luck on Saturday. You've got this. 🚀

---

## **Sources**

- **Agent Factory System of Record**: "OpenClaw with General Agents: A 90-Minute Crash Course" (queried July 22, 2026)
- **Chapter 56**: Meet Your Personal AI Employee (referenced, not quoted)
- **AGENTS.md**: Operational brief included with openclaw-with-general-agents.zip
- **Scenarios 1-8**: Hands-on walkthroughs from Agent Factory crash course

All content sourced from and grounded in the Agent Factory curriculum, available at [panaversity.org/agent-factory](https://panaversity.org/agent-factory).
