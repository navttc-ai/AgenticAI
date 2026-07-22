# Spec-Driven Development: The Art of Agreement Before Action
**A Test-Prep Lesson from Agent Factory**

---

## Opening Hook: The Price of Guessing

Imagine you're building a WhatsApp chatbot for a Pakistani e-commerce seller. You tell an AI, "Create a chatbot that helps customers track their orders." The AI builds something. It works. But then you notice: it tells customers order statuses for *other* customers if they guess an order ID. It crashes when someone sends a photo. It doesn't handle Urdu text. Most painfully, none of it matches how you actually wanted the bot to behave—you were still figuring that out while the AI was already coding.

Now imagine a different scenario: before the AI writes a single line, you sit down and write exactly what "done" means. What information the bot can and cannot see. Which edge cases matter. What's out of scope. You and the AI agree on this in writing. Then the AI builds to that agreement, not to guesses. The cost of fixing a mistake drops from "delete code and start over" to "fix one sentence and re-read the spec."

**That difference has a name: Spec-Driven Development.** And if you're building anything you intend to keep—anything other people will use, maintain, or depend on—this is the discipline that separates systems that work from systems that fail at scale.

This lesson teaches you how to write that agreement, why it matters for AI, and how to use it with both humans and AI agents to build things reliably. By the end, you'll understand why the spec is the product and code is just what falls out of it when you're clear about what you want.

---

## The Core Concept: Thinking First, Building Second

### In Plain English

Spec-Driven Development means you **decide what you want before asking for it to be built**, and you write that decision down clearly enough that nobody—human or AI—can misunderstand it.

Most people work backward: they ask for something, look at what arrives, react to what went wrong, ask for a fix, react again. This is called **vibe coding**, and it feels fast (you see code immediately) but costs you later (every fix introduces new bugs, the result never matches what was in your head, and changing it becomes dangerous because you no longer trust it).

SDD flips this: you spend time upfront thinking, writing, and agreeing on what "done" means. Then the building part is mostly mechanical—the AI (or human) is executing an agreement, not guessing at one.

### Real-World Analogy

Think of it like commissioning a wedding dress. A vibe approach: you describe your vision, the designer starts sewing, you see a rough draft, say "more beading," they adjust, you see it, say "different neckline," they rip out seams, try again. The dress ships months late, costs three times what you budgeted, and you're still not sure it's what you wanted.

The SDD approach: you and the designer spend a day writing down exactly what you want. Sketches, fabric swatches, measurements, the works. The spec is so clear that a different designer could read it and make the same dress without asking a single question. *Then* the sewing starts, and it goes smoothly because there's no guessing involved.

The AI is the second designer: the better your spec, the less the AI has to guess, and the more likely you get what you actually meant.

### The Technical Definition

Spec-Driven Development is a methodology where:

1. **The spec is the source of truth.** It documents the *what* and *why*, not the *how*. "Users can reset a forgotten password via an emailed link that expires in 30 minutes" is a spec. "Use a JWT token in a Postgres table" is implementation, which comes later.

2. **Code is generated from the spec.** Once you agree on the spec, you ask an AI (or write) code that satisfies it. The code is a *build output*, not the source of truth.

3. **The spec lives longer than the code.** When requirements change, you update the spec first, then re-derive the code. This means the spec never gets thrown away; it grows with your project.

### Why This Matters

For three hundred years, software worked backward: you wrote a brief, built it, and discarded the brief. But now, AI can generate code from a clear written spec. That changes everything. If you're working with AI (which you increasingly are), a clear spec is the difference between "I got exactly what I asked for" and "this is close but I can't trust it."

There's also an economic reason: Anthropic studied ~400,000 agentic coding sessions. They found that **planning decisions (deciding what to build) were 70% the person's job; execution decisions (how to build it) were 80% the AI's job.** SDD is how you get good at your 70%. You can't offload your thinking to the AI; you can only make your thinking clear enough that the AI executes it well.

### One Concrete Example: The E-Commerce Seller's Chatbot

Our Pakistani seller wants a WhatsApp chatbot that:
- Shows product details when customers ask "What's in stock?"
- Lets customers check order status using their order ID
- Handles refund requests by collecting reasons and escalating to a human
- Works in Urdu and English

A vibe approach: "Build a customer service chatbot." Weeks later, you get something that half-works, doesn't handle refunds the way you imagined, and shows the same product in stock after you've sold the last one.

An SDD approach:

```markdown
## Chatbot Spec

### Goal
Reduce customer support load by 60% by automating common questions.

### Functional Requirements
- FR-1: Customers can ask "What's the price of [product]?" and get the current price
- FR-2: Customers can say "Check my order" + order ID; bot shows status  
- FR-3: Refund requests trigger a form (reason, screenshot if damaged)
- FR-4: Bot escalates to human agent if refund is approved pending policy review

### Edge Cases
- Product doesn't exist → "That product isn't currently available"
- Order ID is invalid → "I couldn't find that order. Double-check the ID?"
- No screenshot provided for damage claim → "Please send a photo for our records"

### Out of Scope
- Custom product recommendations
- Bulk order negotiations
- Warranty claims (human-only)

### Acceptance Criteria
- [ ] 5 different products can be queried; prices are current
- [ ] 3 test orders can be checked by ID
- [ ] Refund form collects all required fields
- [ ] Responds in Urdu and English
- [ ] A send failure doesn't crash; user gets "Try again in 30 seconds"
```

Now when you give this to Claude or OpenCode, they know exactly what to build. There's no guessing. When something isn't working in your spec, the conversation about "did you miss a requirement?" is fast and cheap (a fixed sentence). When something isn't working in the code, you know it's an implementation bug, not a misunderstood requirement.

---

## Deep Dive: The Three Key Concepts

### Concept 1: Vibe Coding vs. Spec-Driven Development — Why Timing Matters

#### The Idea

The difference between SDD and vibe coding is **when you do your thinking**.

In vibe coding, you think *while* the AI builds. You say "build me X," it shows you code, you react by saying "actually, more like Y," it changes the code, you see the result and realize you meant Z. Each round trip costs a little context, fills in gaps with reasonable-but-wrong assumptions, and the result rarely fits your actual system. The real cost lands later: when you try to change it, trust it, or integrate it with other things.

In SDD, you think first and write it down. The AI doesn't start building until you've said "here's what done means, here's when you'll be right." The build is then mostly mechanical: executing an agreement, not guessing at one.

#### How It Works

**The vibe loop** (no shared "done"):
1. You: "Build a login system"
2. AI builds something with email + password
3. You: "Wait, add Google login too"
4. AI adds Google OAuth  
5. You: "Actually, phone numbers instead of email?"
6. AI rips out the email code and adds SMS
7. You: "This still doesn't handle [thing you forgot]"
8. → Back to step 4, a dozen times

**The SDD loop** (shared "done" first):
1. You: Write a spec covering all three (email, Google, SMS) plus edge cases
2. You & AI: Clarify any ambiguities in the spec
3. AI: Build once, against the spec
4. You: Verify it matches the spec; if a requirement was vague, fix the spec, not the code

The SDD loop is longer at the start (thinking takes time), but the build part is faster, cleaner, and you get what you asked for.

#### Practical Example: Building a Grade-Submission System for a School

**Vibe approach:** A teacher asks Claude to build a form where students submit homework grades. Claude builds a form. The teacher sees it and notices:
- It doesn't prevent students from submitting after the deadline
- It allows negative grades (a clear bug, but was never stated)
- It doesn't group submissions by student
- Each change requires back-and-forth

Total time: 3 hours of chasing requirements that should have been clear from the start.

**SDD approach:** The teacher writes:

```markdown
## Spec: Grade Submission System

### Functional Requirements
- FR-1: Form opens 7 days before deadline, closes at deadline (UTC)
- FR-2: Students can enter grades 0–100 only
- FR-3: Submissions are grouped by student name; duplicates overwrite the previous
- FR-4: Teachers see a summary: "80 of 150 students submitted"

### Edge Cases
- Student submits at 11:59 PM on deadline day → accepted
- Student tries to submit 11:59:01 PM → rejected with "Submission closed"
- Student submits -5 as a grade → rejected with "Grades must be 0–100"

### Out of Scope
- Late submission appeals (handled by email)
- Extra credit (future feature)
```

With this spec, Claude builds it right once. Total time: 30 minutes thinking + 10 minutes building.

#### Common Pitfall: "The spec is just documentation; code is the real thing"

**The mistake:** Many people think specs are nice-to-have documentation, while code is what actually matters. So they skip the spec, build to a vague idea, and treat the spec as something to write after (which never happens) or not at all.

**Why it's wrong:** A spec you write before building is a **test of your thinking**. If you can't write it clearly, you don't understand the problem well enough yet. The code you build without a spec is solving a fuzzy problem, which is why half the requests come back as "wait, it should also…"

**How to fix it:** Flip your mindset. The spec is the product. The code is the build output. If the requirement changes, you change the spec, then re-derive the code. This is why specs matter: they survive the code.

#### Stop & Think 🤔

- **Q1:** Think of something you built recently (code, a process, a system). If you had written a spec first, what misunderstandings would it have caught?
- **Q2:** Vibe coding feels faster at first (code appears immediately), but when does the cost actually land? Try to identify one project where the cost landed late and painful.
- **Q3:** Why does Anthropic's data show that 70% of planning decisions fall to the person, while 80% of execution decisions fall to the AI? What does that imply about where your effort should go?

*Suggested reflection time: 5-7 minutes*

---

### Concept 2: The Spec Is Not the Code; Don't Mix the Two

#### The Idea

A spec describes **what** must be true and **why**. It never describes **how** to build it. That's the critical boundary that keeps the spec alive and useful.

"Users can reset a forgotten password via an emailed link that expires in 30 minutes" is a spec. It describes what has to happen. "Use a JWT token stored in a Postgres `tokens` table" is an implementation detail. It describes how you might do it.

When you mix them, you lock in a technical choice before you've even decided if the behavior it's supposed to deliver is right. A year later, you need to migrate off Postgres. Now you have to rewrite the spec to un-lock the JWT choice. If you'd kept them separate, you'd just update the implementation.

#### How It Works

**A good spec answers three questions:**

1. **Why:** What problem are we solving, and for whom? (The thing most vague conversations never state)
2. **What:** What must be true when this is done? Behaviors, inputs, outputs, rules, edge cases, and explicitly what's out of scope
3. **What not to build:** The boundaries. This single section prevents "it did too much" and "it did the wrong thing" failures

**Notice what's missing: the how.**

A spec is finished not when there's nothing left to add, but when there's nothing left to **misread**.

#### Practical Example: Building an E-Commerce Refund Policy

**Vague spec (mixing what and how):**
```
Customers can request refunds via email. We'll use a Stripe webhook 
to check if the refund is valid. If valid, credit their payment 
method automatically.
```

This locks in Stripe. It never says what "valid" means. It doesn't cover "what if the refund fails?" or "how long does the refund take?"

**Clear spec (behavior only):**
```
## Refund Spec

### Functional Requirements
- FR-1: Customers can request a refund within 30 days of purchase
- FR-2: A refund is valid if: the purchase is within 30 days, 
         the order is marked "delivered," and the customer owns 
         the order (verified by email)
- FR-3: Once approved, the refund is credited to the original 
         payment method within 3–5 business days
- FR-4: Customer gets an email receipt confirming the refund

### Edge Cases
- Request after 30 days → "Refunds expire after 30 days"
- Refund processing fails → "There was an issue. We'll contact you in 24h"

### Out of Scope
- Exchanges (separate process)
- Refunds for items not received (escalate to support)
```

This describes the *behavior*. Implementation is free to change: swap Stripe for PayPal, use a different message queue, change the database. The behavior is the real product.

#### Common Pitfall: "The spec is too vague to prevent misunderstandings"

**The mistake:** A developer writes a vague spec, an AI builds something partially wrong, and then the developer says "the spec wasn't good enough; specs don't work."

**Why it's really the developer's fault:** You didn't finish the spec. A spec is done not when you've written everything you know, but when there's nothing ambiguous left. The test is: could a stranger read this and build exactly the right thing without asking a single question?

If they'd ask "wait, what about [edge case]?" then the spec isn't done.

**How to fix it:** Use the interview phase (Concept 3 below). Have the AI interview you to surface ambiguities. Every question it asks is something the spec should have answered.

#### Stop & Think 🤔

- **Q1:** Look at a feature you've seen built (a login, a checkout, a search). What edge cases do you think the builder never asked about, and how did that cause bugs?
- **Q2:** Why is "use a database" not a spec, while "data must persist across server restarts" is a spec?
- **Q3:** Imagine your spec becomes the contract for AI building your system. What would it cost you if the spec was wrong? What would it save you if the spec was clear?

*Suggested reflection time: 5–7 minutes*

---

### Concept 3: Clarify by Interview — The Cheapest Place to Fix Mistakes

#### The Idea

Before building, **turn the questions around**: instead of you instructing the AI, have the AI interview *you* to reveal what the spec left ambiguous.

This is the highest-value step most people skip, because it feels slow (you're answering questions instead of seeing code) but it's exactly when slow is good. The cost of fixing a mistake drops from "rebuild the feature" to "fix one sentence in the spec."

#### How It Works

You give the AI one instruction:

> Before we build anything, interview me about this spec. Ask one question at a time, focusing on ambiguities, missing edge cases, and unstated assumptions. Keep going until you could hand this spec to a stranger and trust they'd build exactly what I mean.

The AI then asks you hard questions. Every one surfaces something you didn't write down. You answer; the AI folds the answers back into the spec. Suddenly the spec is three times more precise, and you haven't written a line of code yet.

#### Practical Example: A Reservation System for a Tutoring Service

Without the interview, your spec says:
```
Students can book tutoring sessions with available tutors.
```

With the interview, the AI asks:

- "Can a student book multiple sessions with the same tutor?"
- "What if a tutor is fully booked? Do students see a waitlist?"
- "Can a student cancel? How far in advance?"
- "What if a tutor cancels on the student?"
- "Timezone handling: is booking in the student's timezone or tutor's?"
- "Can tutors set availability, or is it automatic?"

Each answer gets folded in:

```
### Functional Requirements
- FR-1: A student can book a tutoring session with a tutor 
        who has available slots in the next 7 days
- FR-2: One student-tutor pair per time slot (no double-booking)
- FR-3: Cancellations must be 24 hours in advance, or the slot 
        is forfeited
- FR-4: If a tutor cancels, the student gets first right to 
        rebook with the same tutor; otherwise, get a credit
- FR-5: All booking times are in the student's local timezone, 
        but the tutor views availability in their own

### Edge Cases
- 11:45 PM student books 11:59 PM session → rejected; 
  sessions must be at least 24h away
- Tutor cancels 12 hours before → student gets $5 credit 
  automatically
```

All of this came from *interview questions*. Without the interview, it ships as a vague "students can book" and half the requirements come back as bugs.

#### Common Pitfall: "This feels slow; let's just build"

**The mistake:** Developers skip the interview phase because it feels like overhead. "We're wasting time answering questions; let's just start coding and adapt as we go."

**Why that costs:** A forgotten requirement found during the interview takes 1 minute to add to the spec. The same requirement found in production takes 1 hour to fix (debug, understand the impact, refactor code, test, deploy). Do that five times per project—which is typical—and you've lost more time than the entire interview would have taken.

**How to fix it:** Treat the interview as a *time-saver*, not overhead. It's the cheap place to find mistakes.

#### Stop & Think 🤔

- **Q1:** Describe a feature you've built where a forgotten requirement surfaced late (in testing, after shipping, or when someone else tried to maintain it). How would an interview have caught it?
- **Q2:** The interview works because the AI asks the questions you didn't think to ask. Why would you not think to ask them on your own? What blind spots do you have as a single person?
- **Q3:** Some people say "let's just build and adapt." For what kinds of projects is that philosophy okay, and for what kinds is it a liability?

*Suggested reflection time: 5–7 minutes*

---

### Concept 4: The Constitution — Rules Above Every Spec

#### The Idea

Before you write your first feature spec, write a short document of project-wide rules that every spec and build must follow. This is the **constitution**.

In claude.ai it's your Project instructions. In Claude Code it's `CLAUDE.md`. In your team repo, it's the document everyone reads first. Same idea everywhere: principles, constraints, and conventions that guide every decision.

#### How It Works

A constitution is small and specific. It answers "what is always true here?" Examples:

```markdown
## Constitution — Smart Notes App

### Principles
- Plain language over cleverness; a new contributor should 
  understand any file in 5 minutes
- Prefer existing libraries over custom code
- Every feature ships with its spec in `specs/`

### Constraints
- Never touch `published/` or `src/generated/` (automated output)
- Stack stays to what we already use (propose, don't add, new dependencies)

### Definition of Done
- Behavior matches the spec, edge cases included
- A human has reviewed the diff against the spec before shipping
```

The constitution sits above every feature spec. Ambiguous questions get answered by looking up the constitution first.

#### Practical Example: A School Grading Portal

**Without a constitution:** Every feature asks, "Should we add a new library?" "Should we write this custom, or reuse?" "What counts as done?" Answers vary by who's building.

**With a constitution:**
```
### Principles
- Reliability first; grades are high-stakes data
- Teachers trust it 100% or not at all; partial correctness is 
  not good enough
- Security: student grades never leak to other students

### Constraints
- Stack: Django (existing knowledge), Postgres (existing), 
  no new frameworks
- Per-student audit logs: every grade change logs who changed 
  what and when
- Tests required: 80%+ coverage on grade-critical paths

### Definition of Done
- Spec is clear and reviewed by the principal
- Feature matches spec 100%
- Security review passed
```

Now, when someone designs a new feature (a bulk-grade-upload), they know it must meet those principles and pass those checks. No debate; the constitution already said it.

#### Common Pitfall: "The constitution is too strict; it slows us down"

**The mistake:** Setting a constitution that demands 90% test coverage, security reviews, and written decision records for a weekend hobby project. Then everything feels heavy, and developers ignore it.

**Why it costs:** A constitution should match your stakes. A high-stakes medical app needs more governance than a weekend note-taker. If the constitution is heavier than the stakes, it becomes theater, and nobody follows it.

**How to fix it:** Write a constitution that matches the actual stakes of your work. For a learning project: 3–5 lines about principles and one constraint. For a production system: more detail.

#### Stop & Think 🤔

- **Q1:** What principles does your current project (work, school, hobby) actually follow, even if they're not written? Write down 3–5 of them. Are they consistent?
- **Q2:** If you wrote a constitution for a project and shared it with a teammate, what would they ask you to clarify?
- **Q3:** Why is a written constitution better than just "we'll figure it out as we go"?

*Suggested reflection time: 5 minutes*

---

## Systems Thinking: How These Concepts Connect

These four concepts form one loop. The **constitution** sets the guardrails for every project. Within those guardrails, you **write a spec** (what and why, never the how). The interview phase **clarifies ambiguities** before they become code. Then the **vibe coding vs. SDD** distinction tells you whether you're building to a clear agreement or guessing.

Think of it like building a company:

- **Constitution** = company culture and values (the unchanging principles)
- **Spec** = a job description (what the role is supposed to deliver)
- **Interview/Clarify** = the onboarding conversation (making sure you both understand what success looks like)
- **Vibe vs. SDD** = the difference between an employee who knows what's expected and one who's just winging it

Remove any one piece, and the system breaks:

- No constitution? Every project invents its own standards, and they contradict each other
- No spec? The builder is guessing, and so is the reviewer
- No clarify phase? Requirements surface as bugs instead of questions
- Building to guesses instead of agreement? You'll rewrite everything when requirements change

The magic isn't any single concept; it's that all four work together. A constitution focuses the specs. Specs make the clarify phase productive. Clear specs make the build phase mechanical. And the build phase is where you see if the spec was right.

---

## Real-World Application: Putting It All Together

Let's run the whole discipline on one realistic, complete scenario: **a weekly digest for an e-commerce platform**.

### The Situation

You're running an online store selling handmade crafts. You have 2,000 customers, but only 30% come back more than once. You think: "What if we email customers a weekly digest of new items they might like, personalizing it by their browsing history? That might bring people back."

You ask Claude to build it. Claude builds something. It works, kind of, but:
- It sends digests to customers who've unsubscribed
- A customer with no browsing history gets an empty email
- Digests go out at random times, not Monday mornings
- Nobody knows what goes in the email, so when something goes wrong, debugging is chaos

Now let's run SDD instead.

### Writing the Spec (Phase 2 & 3: Specify + Clarify)

You start by writing a rough spec:

```markdown
## Weekly Digest Spec

### Goal
Re-engage inactive customers by emailing them personalized 
product recommendations once per week.

### Functional Requirements
- FR-1: Email sends on Monday at 8:00 AM in the customer's local timezone
- FR-2: Include only products added in the past 7 days
- FR-3: Personalize by browsing history (show items similar to what they looked at)
- FR-4: No email if the customer has zero browsing history
- FR-5: Respect unsubscribe: anyone who clicked "unsubscribe" gets nothing

### Edge Cases
- No new products this week → skip (no empty email)
- Time zone missing → default to UTC
- Customer has only looked at one category → show similar items from that category
```

Then you run the clarify phase. Claude asks:

- "What if a customer unsubscribed and then resubscribed? Send them again?"
- "The 'similar to' part: how do you define similarity?"
- "Retry if email send fails?"
- "How many recommendations per email?"
- "What if they looked at products but nothing new was added this week?"

Your answers:

```
- Unsubscribe is permanent; resubscribe requires email
- Similar = same category, same price range
- Retry once; after that, log and move on (don't block others)
- 5–8 recommendations, or all if fewer exist
- Show nothing (no email sent)
```

Now the spec is clear:

```markdown
## Weekly Digest — Refined

### Functional Requirements
- FR-1: Monday 8:00 AM in customer's local timezone 
        (default: UTC if missing)
- FR-2: 5–8 products added in the past 7 days, 
        personalized by browsing history (same category, 
        overlapping price range)
- FR-3: One product per email (no duplicates of previous weeks)
- FR-4: Unsubscribed customers never get emails (permanent 
        until they re-subscribe via email link)
- FR-5: A send failure retries once, then is logged

### Edge Cases
- Zero qualifying products → send nothing
- Zero browsing history → send nothing
- Time zone parsing fails → log warning, use UTC
- 30+ products match → send 8, note "X more available"

### Out of Scope
- Customizable frequency (weekly only)
- Manual campaign sends (scheduled only)
```

### Building Against the Spec (Phase 4: Build)

Now Claude (or OpenCode) builds to this spec. You specify each step:

1. **Build the query:** Select products from the past 7 days that match browsing history
2. **Timezone logic:** Look up customer timezone; if missing, use UTC; send at 8:00 AM
3. **Unsubscribe check:** Skip anyone who clicked "unsubscribe"
4. **Email building:** Format the 5–8 products into an email body
5. **Send with retry:** Send the email; if it fails, try once more, then log
6. **Verify against spec:** Does each acceptance criterion pass?

As each step finishes, you check it against the spec. Step 1 turns up: "Wait, what if a product has no category?" You check the spec. The spec doesn't say. This is cheap to fix now: update the spec to say "If category is null, group by price range only" or "Skip those products," then update the code. Done.

If this had emerged after shipping, it's a production bug. But because you clarified first, it's a two-minute fix to a sentence.

### The Outcome

Result: an email system that works reliably because:

1. Everyone (you, Claude, future reviewers) knows exactly what it should do
2. When bugs appear, you can compare them to the spec and know whether it's a requirement problem or code problem
3. When you want to change behavior (say, "send on Thursdays instead"), you change the spec, the code automatically re-derives, and you know what changed and why
4. A new developer can read the spec and maintain the system without asking 20 questions

### Why This Matters to You

This example matters because it shows the real discipline, in real time. The chatbot, the grading system, the refund system, the reservation system—they all follow this same pattern. And the discipline scales: a small feature (one email), a medium project (a full app), or an enormous one (a company's AI workforce) all follow the same loop.

The test-taking skill this teaches you: **When you see a problem, ask yourself "what's the spec?"** If it's vague, you're looking at a vibe-coding failure in progress. If it's clear, you know what to build and how to verify it's right.

---

## Practical Next Steps: You Can Do This This Week

Pick one small project or feature. Run the discipline end-to-end. This week's steps:

### Step 1: Write a Rough Spec (30 minutes)
Pick something you're building or want to build: a feature, a tool, a process. Write down (in plain English, no code):
- Goal: why are we doing this?
- Functional requirements: what must be true?
- Edge cases: what could go wrong?
- Out of scope: what's explicitly not included?

Don't polish it; just get the thoughts out.

**Expected result:** A messy document with 4–6 sections that captures what you think you're building.

### Step 2: Run a Clarify Interview (20 minutes)
Paste your rough spec into Claude and use this prompt:

> Interview me about this spec. Ask one question at a time about ambiguities, edge cases, and assumptions. Keep going until you understand it completely. Don't suggest solutions, just ask clarifying questions.

Answer Claude's questions. Fold the answers back into your spec.

**Expected result:** Your spec doubled in clarity and specificity. Things you forgot to mention now written down.

### Step 3: Show It to a Stranger (15 minutes)
Copy your refined spec. Paste it into a fresh Claude conversation (no context) and ask:

> Based only on this spec, what would you build? Summarize in 3 sentences what you understand.

If Claude's summary matches what you meant, the spec is clear. If it diverges, fix the spec.

**Expected result:** You now have a spec clear enough that an AI (or human) can read it cold and understand your intent.

### Step 4: Build One Small Thing (1–2 hours)
Ask Claude (or a human) to build to your spec. Watch what happens. When the build diverges from the spec, make a note:

- Is the spec vague? (Fix the spec)
- Is the build wrong? (Fix the build)

This trains your judgment about what makes a good spec.

**Expected result:** A working thing that matches what you actually wanted, because you decided first.

### Step 5: Reflect (10 minutes)
Think back: in this project, what misunderstandings did the spec prevent? When did clarity actually save you time?

**Expected result:** Visceral understanding of why SDD works.

---

## Common Questions & Misconceptions

### "Doesn't a spec slow you down?"

**The misconception:** Writing a spec takes time upfront, so SDD is slower than vibe coding.

**The reality:** Upfront time is slower, but total time is faster. You spend 30 minutes clarifying upfront vs. 2 hours rewriting after the build is wrong. A clear spec saves time overall, and more importantly, it saves *debugging* time (the most expensive kind).

### "Isn't the AI supposed to figure out edge cases for me?"

**The misconception:** AI is smart; it should just know what you mean without you having to spell everything out.

**The reality:** AI is smart at executing, not mind-reading. The smarter the AI is, the more a clear spec helps it. A vague request to a smart AI just means it confidently builds the wrong thing. A clear spec lets it confidently build the right thing.

### "What if I don't know all the requirements upfront?"

**The misconception:** If you're exploring and learning, you can't write a spec because you don't know what you want yet.

**The reality:** You have two different phases. **Exploration:** vibe coding, learn fast, throw things away. **Building something real:** switch to SDD. Once you know roughly what you want, write it down, clarify it, build it properly. The mistake is exploring *and* building simultaneously in vibe mode, which gives you a half-understood project.

### "My team doesn't follow specs anyway; they just code."

**The misconception:** Specs are bureaucratic overhead; real builders just build.

**The reality:** Your team codes without a spec, which means they're guessing at requirements. The bugs you catch in code review are requirements that should have been clarified upfront. When the spec is clear, code reviews are about implementation, not "wait, does this do what we wanted?"

The fix: write a one-page spec for your next feature, run the clarify interview, then build. After one project, everyone will see the difference.

### "This works for simple features, but not complex systems."

**The misconception:** SDD is for small projects; big systems are too complicated.

**The reality:** SDD matters *more* for big systems. The bigger the project, the higher the cost of a misunderstanding. A wrong detail in a 100-person system costs months to fix. The same detail caught in the spec costs a sentence.

---

## Why Test Prep Requires Understanding the Discipline

Your test will likely ask you:

1. **Conceptual questions:** "Why does SDD matter for AI-driven development?" Answer: because 70% of the work is *you* deciding what to build, and a clear spec is how you get good at that.

2. **Practical scenarios:** "A team builds a feature, ships it, then discovers a missing requirement. Using SDD, how would this have gone differently?" Answer: The requirement would have surfaced in the clarify interview (cheap fix) instead of production (expensive fix).

3. **Judgment calls:** "You're building a weekend hobby project. Do you need a full SDD process?" Answer: No; size your process to your stakes. A constitution + rough spec is enough.

The underlying skill: **Using SDD means you think about the problem before asking for help.** The test is checking if you understand that rhythm.

---

## Summary: The One-Page Takeaway

### The Core Discipline

**Spec-Driven Development = Thinking First, Building Second**

| What | How | Why |
|------|-----|-----|
| **The Constitution** | Project-wide rules that guide every spec | Consistency; decisions made once, not repeatedly |
| **The Spec** | Written agreement on *what* and *why*, never *how* | Source of truth; code is generated from it, not the other way around |
| **The Clarify Interview** | AI asks you questions to surface ambiguities | Cheapest place to fix mistakes (spec edit vs. code rewrite) |
| **The Build** | Implement to the spec, verifying against it | Code executes an agreement, doesn't guess at one |

### Three Things to Remember for Your Test

1. **The vibe vs. SDD distinction is about when you do your thinking.** Vibe: think while building (slow build phase, lots of rework). SDD: think first (slower spec phase, faster build phase, less rework overall).

2. **The spec is not code.** The spec describes behavior; the code implements it. When behavior changes, change the spec first, then re-derive the code.

3. **The constitution, spec, and interview are how you use the AI well.** AI is 80% execution (following a clear plan). You're 70% planning (deciding what to build). SDD is the discipline that makes that split work.

### The Pattern You'll See on Your Test

Whenever a scenario asks "what goes wrong?" or "how would you prevent that?" the answer often involves SDD:

- Missing requirement → should have been clarified in the spec
- Scope creep → should have been in the "out of scope" section
- Requirements drift → should have been pinned down in the constitution
- Rework → should have been caught in the clarify interview

If you remember that SDD is about thinking first and agreeing on what "done" means before you build, you'll answer most of those questions correctly.

---

**Sources:** Agent Factory System of Record (Spec-Driven Development Crash Course)

**Ready for the test?** The key thing your exam will check: do you understand *why* SDD matters, not just *how* to do it. Why it's faster overall even though it feels slower upfront. Why the spec is more valuable than the code. Why your thinking (the 70%) is the scarce resource, not the AI's building (the 80%). Get that, and the details follow naturally.

You've got this. 🚀
