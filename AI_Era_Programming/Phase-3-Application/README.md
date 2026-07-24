# Phase 3: Application

**Outcome:** "I can give my agent real, custom tools and a real backend"

**Estimated time:** ~11–12 hours
**Dependencies:** Phase 2 complete.

## Lessons

1. **[Lesson-01-Real-World-Python-Files-Packages-Tools.md](Lesson-01-Real-World-Python-Files-Packages-Tools.md)** — Reading/writing real files, organizing code as a package, comprehensions and generators.
2. **[Lesson-02-Concurrency-APIs-and-Automated-Verification.md](Lesson-02-Concurrency-APIs-and-Automated-Verification.md)** — CLI tools, async Python, a first taste of FastAPI, and automated verification. *(Seam lesson — also touches Phase 4 CI/CD content; see note below.)*
3. **[Lesson-03-Advanced-MCP-Servers-I.md](Lesson-03-Advanced-MCP-Servers-I.md)** — Context/lifespan, sampling, progress notifications, and roots.
4. **[Lesson-04-Advanced-MCP-Servers-II.md](Lesson-04-Advanced-MCP-Servers-II.md)** — StreamableHTTP transport, stateful vs. stateless scaling, error handling, and packaging.
5. **[Lesson-05-Production-MCP-Server-to-Skill-Patterns.md](Lesson-05-Production-MCP-Server-to-Skill-Patterns.md)** — The production MCP server capstone, bridging into skill patterns that orchestrate real execution.
6. **[Lesson-06-Building-MCP-Wrapping-and-Script-Execution-Skills.md](Lesson-06-Building-MCP-Wrapping-and-Script-Execution-Skills.md)** — Skills that call MCP tools intelligently or write and run scripts.
7. **[Lesson-07-Full-Workflow-Orchestration-and-Capstone.md](Lesson-07-Full-Workflow-Orchestration-and-Capstone.md)** — Combining MCP calls, scripts, and error recovery into one orchestrated workflow; the shippable-skill capstone.
8. **[Lesson-08-Your-First-FastAPI-Skill.md](Lesson-08-Your-First-FastAPI-Skill.md)** — Building a FastAPI skill first, then your first route, tests, and Pydantic validation.
9. **[Lesson-09-Production-CRUD-Errors-DI-Config.md](Lesson-09-Production-CRUD-Errors-DI-Config.md)** — Full CRUD, explicit error responses, dependency injection, and environment config.
10. **[Lesson-10-Database-Auth-Middleware.md](Lesson-10-Database-Auth-Middleware.md)** — Postgres via SQLModel+Neon, password storage, JWT auth, and middleware. *(Seam lesson — also touches Phase 4 JWT/middleware-infra content; see note below.)*

## Note on seam lessons

Lessons 2 and 10 each contain concepts that span two Curriculum Map phases and were assigned here as the best single-file fit (per `REORGANIZATION-GUIDE.md`). Their Phase 4-adjacent concepts (CI/CD in Lesson 2; deeper JWT/middleware infra in Lesson 10) are relevant again when working through Phase 4.
