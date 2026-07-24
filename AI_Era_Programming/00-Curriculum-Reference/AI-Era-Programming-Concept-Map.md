# AI-ERA PROGRAMMING: COMPLETE CONCEPT MAP
### Python → Custom Agent Development → MCP Development → FastAPI

**Sources**: Agent Factory System of Record (MCP connector) + live fetch of:
- `/docs/Programming-in-the-AI-Era` (Part 4, all 9 phase pages)
- `/docs/Building-Agent-Factories/openai-agents-sdk` (Ch 62 sidebar)
- `/docs/Building-Agent-Factories/mcp-fundamentals` (Ch 66)
- `/docs/Building-Agent-Factories/custom-mcp-servers` (Ch 67)
- `/docs/Building-Agent-Factories/agent-skills-mcp-code-execution` (Ch 68)
- `/docs/Building-Agent-Factories/fastapi-for-agents` (Ch 70)

**Note on grain**: Python and Custom Agent Development are drawn straight from the book's own chapter/lesson structure — the SoR is already organized at roughly the 4-concept working-memory grain the project's pedagogy requires, so this map uses the book's real chapters/lessons as "concepts" rather than re-extracting from scratch.

---

## AREA 1 — PYTHON IN THE AI ERA
**Book location**: Part 4, Chapters 42–71 (9 phases) · **Total concepts (chapters): 30**
**Recommended lessons**: 30 ÷ 4 ≈ **8 lessons** (the book's own 9 phases are close to this already and can be used as the grouping — see Curriculum Map)

| Phase | Role | Chapters | Concepts (1 line each) |
|---|---|---|---|
| 1 · The Workbench | Reader | 42–46 | 42 PRIMM-AI+ Framework — the read/predict/verify learning method · 43 Ten Axioms of AI-Driven Development — the guiding principles · 44 The Development Environment — uv, pyright, ruff, pytest, Git · 45 Reading Python — Predict-Run-Investigate fluency · 46 Your First TDG Cycle — spec → test → generate → verify loop |
| 2 · Specify with Types | Specifier | 47–49 | 47 Primitive Types & Expressions — int/float/str/bool/None · 48 Strings & Collections — list/dict/tuple/set, typed containers · 49 Functions as Contracts — signatures, defaults, docstrings as specs |
| 3 · Tests as Specification | Verifier | 50–55 | 50 Control Flow — if/for/while, decisions & repetition · 51 Data Models with Dataclasses — structured types replace fragile dicts · 52 pytest Deep Dive — fixtures, parametrize, coverage · 53 Iterating on AI Output — the refine/re-generate feedback loop · 54 Error Handling & Exceptions — try/except/raise/finally · 55 Validation with Pydantic — BaseModel at system boundaries |
| 4 · Debug & Master | Debugger | 56–57 | 56 Debugging AI-Generated Code — tracebacks, print-debugging, 5 AI failure patterns, the 5-step loop · 57 TDG Mastery — driving the full cycle with no scaffolding |
| 5 · The Python Object Model | Modeler | 58–61 | 58 Classes & Instances — `__init__`, self, dataclass vs class · 59 Inheritance, Composition & Design — is-a vs has-a, ABCs · 60 Special Methods — `__repr__ __eq__ __iter__ __len__` etc. · 61 Decorators, Properties & Advanced Patterns — `@property`, Protocols, DI |
| 6 · Real-World Python | Practitioner | 62–64 | 62 Files & Data Processing — pathlib, JSON, CSV, pipelines · 63 Modules & Packages — `__init__.py`, import mechanics · 64 Comprehensions, Generators & Functional Patterns — list/dict/set comps, `yield`, key functions |
| 7 · CLI & Concurrency | Tool Builder | 65–67 | 65 Unix-Style CLI Tools — argparse, subcommands, exit codes · 66 Async Python — async/await, `asyncio.gather`, pytest-asyncio · 67 FastAPI: Your First Web API — first taste of routes + Pydantic models |
| 8 · Production Systems | Shipping Engineer | 68–69 | 68 CI/CD, Git Workflows & Observability — automated verification pipelines · 69 Security Review for AI-Generated Code — auditing what AI misses |
| 9 · Capstone | Architect | 70–71 | 70–71 SmartNotes finished end-to-end + QuizForge — an independent second project proving transfer |

**Dependencies**: strictly linear, Phase 1→9. Phase 2 needs Phase 1 (reading before specifying); Phase 5 (OOP) needs Phase 4 (debugging discipline); Phase 7 (async/FastAPI taste) needs Phase 6 (files/modules).

---

## AREA 2 — CUSTOM AGENT DEVELOPMENT
**Book location**: Part 6, Chapters 61–65 · **Total concepts: 5 chapters** (2 broken out to lesson-level below; Ch 63–65 available at chapter grain — ask if you want their lesson-level breakdown pulled too)

| Ch | Title | Concepts |
|---|---|---|
| 61 | Introduction to AI Agents (conceptual, no build yet) | What Is an AI Agent? · Core Agent Architecture (Model/Tools/Orchestration/Deployment) · The Agentic Problem-Solving Loop (Get Mission→Scan→Think→Act→Observe) · Multi-Agent Design Patterns (Coordinator/Sequential/Iterative/HITL) · Agent Ops (LM-as-Judge, tracing, golden datasets) · Agent Interoperability & Security (A2A, Agent Cards) · The Agent SDK Landscape · Your First Agent Concept |
| 62 | OpenAI Agents SDK (**BUILD phase** — production agent architecture) | Build Your OpenAI Agents Skill · SDK Setup & First Agent · Function Tools & Context Objects · Agents as Tools & Multi-Agent Orchestration · Agent Handoffs & Message Filtering · Guardrails & Agent-Based Validation · Sessions & Conversation Memory · Tracing, Hooks & Observability · MCP Integration (external tools) · RAG with FileSearchTool · Capstone: Customer Support Digital FTE |
| 63 | Building Custom Agents with Google ADK | Google's alternative agent framework — architecture parity check against Ch 62 patterns |
| 64 | The Claude API — Agentic Loops, Structured Output & Batch Processing | Building agentic loops directly on the Claude API; structured output; batch processing at scale |
| 65 | Anthropic Claude Agent SDK | Anthropic's own agent SDK — a third framework option, compared against Ch 62/63 |

**Dependencies**: Ch 61 (concepts) → Ch 62 (build with OpenAI SDK) → Ch 63/64/65 (comparative frameworks) → feeds directly into MCP (Ch 66, which assumes SDK tool-use experience) and FastAPI (Ch 70, which assumes Agent/Runner/function_tool from Ch 62).

---

## AREA 3 — MCP DEVELOPMENT
**Book location**: Part 6, Chapters 66–68 · **Total concepts: 25 lessons across 3 chapters**

| Ch | Title | Lessons (concepts) |
|---|---|---|
| 66 | MCP Fundamentals (~2h) | MCP Architecture Overview (Host-Client-Server) · Transport Layers (stdio vs Streamable HTTP) · Tools — the model-controlled primitive · Resources — the app-controlled primitive · Prompts — the user-controlled primitive · Configuring MCP Clients · Using Community MCP Servers · Debugging & Troubleshooting MCP |
| 67 | Advanced MCP Server Development (~production patterns) | Context Object & Server Lifespan · Sampling — servers calling LLMs · Progress & Logging Notifications · Roots — file system permissions · StreamableHTTP Transport (SSE, sessions) · Stateful vs Stateless Servers (scaling) · Error Handling & Recovery · Packaging & Distribution · Capstone: Production MCP Server |
| 68 | Agent Skills & MCP Code Execution (~6h) | Advanced Skill Patterns · Skill Composition & Multi-Skill Workflows · Anatomy of MCP-Wrapping Skills · Build Your MCP-Wrapping Skill · Script Execution Fundamentals · Build Script-Execution Skill · Full Workflow Orchestration · Capstone: Shippable Agent Skill |

**Dependencies**: Ch 66 (fundamentals: decorators, primitives) → Ch 67 (production patterns — explicitly builds on 66, skips re-teaching `@mcp.tool`/`@mcp.resource`/`@mcp.prompt` basics) → Ch 68 (wraps MCP servers inside Skills; needs both 66 and 67). Also needs Ch 62–65 (agent/tool-use experience) as prerequisite.

---

## AREA 4 — FASTAPI FOR AGENTS
**Book location**: Part 6, Chapter 70 · **Total concepts: 16 lessons**

Skill-First pattern: Lesson 0 builds a `fastapi-agent-api` skill *before* teaching FastAPI; the rest of the chapter is understanding and hardening what was built.

**Lessons 1–10 (Task API core)**: Build Your FastAPI Skill · Hello FastAPI · Pytest Fundamentals · POST and Pydantic Models · Full CRUD Operations · Error Handling · Dependency Injection · Environment Variables · SQLModel + Neon Setup · User Management & Password Hashing

**Lessons 11–15 (infra + agent integration)**: JWT Authentication · Middleware & CORS · Lifespan Events · Streaming with SSE · Agent Integration (the key insight: *APIs are functions, functions become tools, agents use tools*)

**Lesson 16**: Capstone — Agent-Powered Task Service

**Dependencies**: Chapter 3 skills (skill-creator) · Ch 62 (Agent/Runner/function_tool) · Ch 66–67 (HTTP/SSE patterns from MCP) · Part 4 Python (async/await, type hints, Pydantic).

---

## GRAND TOTAL
| Area | Concepts | Recommended lessons (÷4) |
|---|---|---|
| Python in the AI Era | 30 | 8 |
| Custom Agent Development | 5 chapters (19+ lessons where broken out) | 5–7 depending on grain |
| MCP Development | 25 | 7 |
| FastAPI for Agents | 16 | 4 |
| **Total** | **~76 concepts / lessons** | **~24–26 lessons** |

At ~55 min/lesson (this project's Study Session protocol), that's roughly **22–24 hours of focused study** across all four areas — realistically a 2–3 month part-time program.

## REAL-WORLD SCENARIO SUGGESTION
Thread a single build through all four areas, consistent with this project's existing "Digital FTE" thread: **build a production customer-support agent (a ShopBot-style "FatimaBot")** —
1. Python gives you the verified, typed foundation (SmartNotes teaches the discipline; FatimaBot is where you apply it)
2. Custom Agent Dev gives you the agent itself (OpenAI Agents SDK, handoffs, guardrails)
3. MCP Dev gives it real tools (a `customer-data` MCP server: lookup_customer, refunds, ticket search)
4. FastAPI gives it a shippable surface (a Task/Ticket API the agent calls, deployed behind auth)
