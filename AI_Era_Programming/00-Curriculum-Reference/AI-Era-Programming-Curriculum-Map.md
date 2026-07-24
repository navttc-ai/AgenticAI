# AI-ERA PROGRAMMING CURRICULUM MAP
### Python → Custom Agent Development → MCP Development → FastAPI

**Big Picture**: A ~24-hour, self-paced path that takes you from reading AI-generated Python to shipping a production Digital FTE — a customer-support agent with real tools (MCP), a real API surface (FastAPI), and a real framework (OpenAI Agents SDK). Built entirely on Agent Factory content (Parts 4 & 6).

**Who this is for**: Learner going from ~zero/light Python to "I can build and ship an AI agent." Self-paced, 1–2 hrs/day.

**Prerequisite knowledge**: Basic terminal use; Claude Code / Spec-Driven Development familiarity helps but Phase 1 below covers it if not.

**Real-world scenario threading all 4 areas**: **Building "FatimaBot" — a customer-support Digital FTE.** Python builds the verified foundation (SmartNotes project). Agent Dev builds FatimaBot's brain. MCP gives it real tools (customer lookups, refunds). FastAPI gives it a shippable, authenticated surface other systems can call.

---

## PHASE 1: FOUNDATION — "I can read AI-generated code and think like an agent architect"
*(Python Phases 1–2, ~15 chapters worth of reading fluency + Custom Agent Ch 61 concepts)*

| Block | Source | Concepts | Outcome |
|---|---|---|---|
| 1.1 Read & Explore | Python Ch 42–46 | PRIMM-AI+ · Ten Axioms · Dev Environment · Reading Python · First TDG Cycle | Can read AI-generated code fluently and run one full spec→test→generate→verify loop |
| 1.2 Specify with Types | Python Ch 47–49 | Primitive types · Strings/Collections · Functions as Contracts | Can write type-annotated function signatures AI can't misread |
| 1.3 Agent Concepts | Custom Agent Ch 61 (8 lessons) | Agent taxonomy · 3+1 architecture · Operational loop · Multi-agent patterns · Agent Ops · Interop/security · SDK landscape | Understands agent architecture well enough to evaluate any framework before writing a line of agent code |

**Why this phase**: You cannot specify code you cannot read, and you cannot build an agent you cannot describe architecturally. Both threads start conceptual before either goes hands-on.

**Dependencies**: None — this is the true starting point.

**Estimated time**: ~7–8 hours

---

## PHASE 2: MECHANICS — "I can verify Python and build a working agent"
*(Python Phases 3–5 + Custom Agent Ch 62–65 + MCP Ch 66)*

| Block | Source | Concepts | Outcome |
|---|---|---|---|
| 2.1 Tests as Spec | Python Ch 50–55 | Control flow · Dataclasses · pytest · Iteration on AI output · Error handling · Pydantic | Can write a full test suite that *is* the specification |
| 2.2 Debug & Master | Python Ch 56–57 | Debugging AI code · TDG independence | Can drive the full TDG cycle with zero scaffolding |
| 2.3 The Object Model | Python Ch 58–61 | Classes · Inheritance/Composition · Special methods · Decorators/Protocols | Can design a system of objects, not just scripts |
| 2.4 Build the Agent | Custom Agent Ch 62 (11 lessons) | SDK setup · Function tools · Multi-agent orchestration · Handoffs · Guardrails · Sessions · Tracing · MCP integration · RAG · Capstone FTE | **Working Customer Support Digital FTE** exists (pre-MCP-tools) |
| 2.5 Framework Comparison | Custom Agent Ch 63–65 | Google ADK · Claude API agentic loops · Claude Agent SDK | Can justify a framework choice instead of defaulting to the first one learned |
| 2.6 MCP Fundamentals | MCP Ch 66 (8 lessons) | Host-Client-Server · Transports · Tools/Resources/Prompts · Client config · Community servers · Debugging | Can use *any* existing MCP server confidently |

**Why this phase**: This is the longest, densest phase — Python's hardest concepts (OOP) plus the actual agent build plus MCP fundamentals. Budget the most time here.

**Dependencies**: Phase 1 complete (both threads).

**Estimated time**: ~10–11 hours

---

## PHASE 3: APPLICATION — "I can give my agent real, custom tools and a real backend"
*(Python Phases 6–7 + MCP Ch 67–68 + FastAPI Lessons 1–10)*

| Block | Source | Concepts | Outcome |
|---|---|---|---|
| 3.1 Real-World Python | Python Ch 62–64 | Files/data processing · Modules/packages · Comprehensions/generators | SmartNotes persists and is a proper package |
| 3.2 CLI & Concurrency | Python Ch 65–67 | CLI tools · Async Python · First FastAPI taste | SmartNotes is a real, async-capable tool |
| 3.3 Advanced MCP Servers | MCP Ch 67 (9 lessons) | Context/lifespan · Sampling · Progress notifications · Roots · StreamableHTTP · Stateful/stateless · Error handling · Packaging | **Custom `customer-data` MCP server**, production-grade, wired into FatimaBot |
| 3.4 Agent Skills + Code Execution | MCP Ch 68 (8 lessons) | Advanced skill patterns · MCP-wrapping skills · Script execution · Workflow orchestration · Capstone | A shippable skill that autonomously orchestrates MCP + scripts |
| 3.5 FastAPI Core | FastAPI Lessons 1–10 | Skill-first build · Routes · Pytest · Pydantic models · CRUD · Errors · DI · Env vars · SQLModel+Neon · Auth basics | A working, tested, database-backed Task API |

**Why this phase**: Everything you learned conceptually becomes real infrastructure — a custom tool server and a real API, both feeding the agent built in Phase 2.

**Dependencies**: Phase 2 complete.

**Estimated time**: ~11–12 hours

---

## PHASE 4: MASTERY — "I can ship it, secure it, and connect it end-to-end"
*(Python Phase 8–9 + FastAPI Lessons 11–16)*

| Block | Source | Concepts | Outcome |
|---|---|---|---|
| 4.1 Production Systems | Python Ch 68–69 | CI/CD & observability · Security review | Automated pipeline; audited for what AI misses |
| 4.2 Capstone | Python Ch 70–71 | SmartNotes finished + QuizForge (independent 2nd project) | Two portfolio-grade projects proving transfer |
| 4.3 FastAPI Infra + Agent Integration | FastAPI Lessons 11–16 | JWT auth · Middleware/CORS · Lifespan events · SSE streaming · **Agent Integration** · Capstone | **FatimaBot's API is live**: authenticated, streaming, and the agent calls it as a tool |

**Why this phase**: This is where the three build threads (SmartNotes/QuizForge, FatimaBot the agent, the customer-data MCP server, the Task API) converge into one shippable, secured system.

**Dependencies**: Phases 1–3 complete.

**Estimated time**: ~5–6 hours

---

## TOTAL & PACING
- **Total study time**: ~33–37 hours of focused material (longer than a typical 3–4hr AF crash-course curriculum because this spans 4 full deep-dive areas, not 4 lessons)
- **Recommended pace**: 5–6 hours/week → **6–7 weeks**, or 1–2 hrs/day → **~2.5 months**
- **Checkpoint discipline**: use this project's Study Session System (Prime→Deep Read→Lock→Teach→Break) per lesson-sized chunk within each block above; don't try to "session" an entire Phase at once — each numbered block (1.1, 2.4, 3.3, etc.) is itself lesson-sized and should get its own session(s).

## CONNECTIONS FORWARD
Completing this curriculum sets up (not included here): Ch 69 Multi-Agent Reliability, Ch 71–72 ChatKit/Apps SDK distribution, Ch 73–78 (RAG, SQLModel depth, memory, evals, knowledge graphs), and Part 7 Cloud Deployment — the natural next curriculum once FatimaBot is built and tested.

## NEXT STEP
Per this project's workflow, the next move is **Lesson Generator**, run block-by-block (e.g., start with Block 1.1) to produce actual 1300–1500-word study-ready lessons — or, if you'd rather regroup any block above into stricter 4-concept lesson chunks first (some blocks like 2.4's 11 lessons or 3.3's 9 lessons exceed the working-memory-4 rule as single lessons), say the word and I'll re-run Concept Extractor-style chunking on that specific block before generating lessons.
