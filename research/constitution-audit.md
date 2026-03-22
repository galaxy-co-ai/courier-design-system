# Constitution Audit — Transferable Patterns for Courier

**Source:** Dalton's engineering constitution (`C:/.ai/`)
**Purpose:** Extract universal patterns a bootstrapped technical founder can use, adapted for Courier's domain and stack.
**Date:** 2026-03-21

---

## 1. TRANSFERABLE PATTERNS

### 1A. Decision Framework

The four-question decision filter is stack-agnostic and immediately useful:

1. **Does it align with priorities?** — Prevents scope creep, which kills solo/small-team projects.
2. **What's the cost of reversing?** — Separates one-way doors (architecture, public APIs) from two-way doors (naming, UI, config). Cheap to reverse = move fast.
3. **Is it hard, or is it wrong?** — Forces honesty about whether resistance is complexity (push through) or a signal the approach is flawed (stop and rethink).
4. **Will future-me thank present-me?** — The maintenance empathy check. Especially relevant for infra projects where "future-me" is 3am-debugging-production-me.

### 1B. Priority Stack

`Health/Family > Focus projects > Learning > Maintenance > Exploration`

This is a personal triage system disguised as a priority list. The key insight: **exploration is last**, which prevents the common founder trap of endless R&D that never ships. Learning sits above maintenance — you invest in yourself before you maintain what exists.

### 1C. Quality Tiers

Two explicit tiers prevent perfectionism on throwaway work and sloppiness on user-facing work:

- **"Good enough"** — drafts, experiments, internal tools, prototypes
- **"Production quality"** — anything a user touches, any public API surface

The power is in having the boundary stated, not implied. Most founders either over-polish everything or under-polish everything because the line was never drawn.

### 1D. When Stuck Protocol

`Zoom out → Break down → Timebox 25min → Ask for help → Walk away`

This is an escalation ladder, not a checklist. Each step is a fallback for when the previous one didn't work. The timebox is critical — it converts "I'm stuck" from an emotional state into a bounded experiment.

### 1E. Failure Philosophy

"Failure is data, not identity."

One sentence. No elaboration needed. This belongs in every founder's constitution because the alternative — internalizing failure — is the #1 reason technical founders burn out.

### 1F. Focus Protection

Core principles that transfer directly:

- **Attention is the scarcest resource** — not time, not money, not compute.
- **Context switching costs 15-30 minutes** — making this explicit changes behavior. You stop "quickly checking" things.
- **Every "yes" is a "no" to something else** — opportunity cost made visceral.
- **When overwhelmed: Stop, List, Prioritize, Scope down, Start** — another escalation ladder. "Scope down" is the non-obvious step most people skip.

### 1G. Organization Philosophy

- **Find it in 10 seconds or it's in the wrong place** — testable heuristic, not vibes.
- **3-level max nesting** — prevents the deep hierarchy trap where things get "organized" into oblivion.
- **Naming is documentation** — if you need a README to explain what a folder contains, the folder name is wrong.
- **Archive, don't delete** — low-cost insurance against "I actually needed that."

### 1H. Code Philosophy (Stack-Agnostic Parts)

- **Explain "why" not "what"** in comments — universal across all languages.
- **Error handling: always with context, never swallow silently** — especially critical for infra/backend work where silent failures cascade.
- **No commented-out code** — that's what git is for.
- **TODOs with context** — naked `TODO` is tech debt that nobody pays. `TODO(reason, date)` is actionable.

### 1I. Core Beliefs (Philosophy Layer)

- **Build to last, not to launch** — anti-"move fast and break things." Relevant for infra projects where breaking things means breaking everyone downstream.
- **Organized chaos beats perfect paralysis** — permission to be messy as long as you're moving.
- **Tools serve people, not reverse** — prevents tool worship. You don't adopt a framework because it's trendy; you adopt it because it solves your problem.
- **Understanding beats memorization** — invest in mental models, not rote patterns.
- **Done is a direction, not a destination** — everything is iteration. Ship, learn, improve.

---

## 2. STRUCTURE ANALYSIS

### How the Constitution Is Organized

```
.ai/
├── foundation/          ← WHO you are, HOW you decide
│   ├── principles.md    ← Decision framework, beliefs, quality tiers
│   ├── identity.md      ← Personal context, working style
│   ├── organization.md  ← How things are structured
│   └── focus.md         ← Attention management
├── tools/               ← WHAT you build with
│   ├── approved-stack.md
│   └── development/
│       └── coding-standards.md
└── (projects/, systems/) ← WHERE the work happens
```

### What Makes This Effective

1. **Separation of philosophy from implementation.** Principles don't mention TypeScript. Coding standards don't philosophize. This means the philosophy layer survives stack changes — you can swap Next.js for Django and the decision framework still works.

2. **Escalation ladders instead of rigid rules.** "When stuck" and "when overwhelmed" are graduated responses, not binary switches. This matches how humans actually problem-solve.

3. **Testable heuristics.** "Find it in 10 seconds" and "3-level max nesting" are verifiable. Compare to vague advice like "keep things organized."

4. **Explicit quality boundaries.** Two tiers with clear triggers. No ambiguity about when to polish vs. when to ship rough.

5. **Identity layer exists but is thin.** It provides context without being a manifesto. An AI assistant reads it and knows "evening worker, self-taught, prefers understanding over memorization" — enough to calibrate, not so much that it constrains.

### What a New Founder Needs vs. Nice-to-Have

**Essential (build first):**
- Principles/decision framework — this is the CPU of the whole system
- Coding standards — even a short one prevents drift
- Approved stack — prevents "let me try this new thing" on production projects
- Focus rules — especially if working solo with no external accountability

**Important but can grow organically:**
- Organization philosophy — useful once you have enough things to organize
- Identity context — helps AI assistants calibrate but not urgent day one

**Nice-to-have:**
- Project-specific contexts — build these as projects mature
- Systems documentation — emerges from doing, not from planning

---

## 3. ADAPTATION NOTES

### 3A. Decision Framework → Courier

Transfers directly with zero modification. The four questions are language-agnostic and domain-agnostic. For Courier specifically, the "cost of reversing" question is especially important because:

- **API compatibility decisions are one-way doors.** Once external users depend on an endpoint shape, changing it is expensive.
- **Model serving architecture is hard to reverse.** MLX vs. other backends, memory management strategies, streaming protocols — these are foundational.
- **Database schema (CourierDB, FlowDB) is medium-cost to reverse.** Migrations exist but are painful at scale.

**Adaptation:** Add a Courier-specific corollary: "If it touches the API contract or model loading pipeline, it's a one-way door. Treat accordingly."

### 3B. Priority Stack → Courier

The structure transfers. The content shifts:

`Health/Sustainability > Courier core (model serving, API) > Learning (ML/infra) > Maintenance (deps, docs) > Exploration (new model support, experimental features)`

**Adaptation:** Jackson's "focus projects" is singular — Courier is the company. The priority stack should reflect *within-product* priorities: core reliability > new capabilities > polish.

### 3C. Quality Tiers → Courier

Same two tiers, different boundaries:

- **"Good enough":** Internal scripts, dev tooling, experimental model support, benchmarks
- **"Production quality":** API endpoints, model serving pipeline, CourierDB/FlowDB operations, anything a developer integrates against

**Adaptation:** Add a third tier for Courier: **"Battle-tested"** — the model loading and inference hot path. This code gets more scrutiny than normal production code because failures here mean models don't serve, which is the entire product.

### 3D. Coding Standards → Python

The philosophy transfers. The specifics change entirely:

| Dalton's Standard | Courier Equivalent |
|---|---|
| TypeScript strict mode | Python type hints + `mypy` strict or `pyright` |
| PascalCase components | PascalCase classes, snake_case everything else (PEP 8) |
| 2 spaces, semicolons | 4 spaces, no semicolons (PEP 8 / Black formatter) |
| Named exports | Explicit `__all__` in `__init__.py` |
| Functional components | Dataclasses / Pydantic models over raw dicts |
| shadcn/ui patterns | FastAPI/Uvicorn patterns |

**Transferable as-is:**
- "Explain why, not what" in comments
- Error handling with context, never swallow silently → especially critical for model loading failures
- No commented-out code
- TODOs with context
- Import ordering (stdlib → third-party → local)

**Adaptation:** Python has stronger conventions (PEP 8, Black, isort) than the JS ecosystem. Jackson's standards doc can be shorter because more is already decided by the language community. Focus the doc on *project-specific* decisions: how to structure model backends, API versioning conventions, CourierDB query patterns.

### 3E. Approved Stack → Courier

Dalton's stack doc prevents tool churn. Courier needs the same thing for a different ecosystem:

| Layer | Courier Equivalent |
|---|---|
| Framework | FastAPI + Uvicorn |
| Language | Python 3.11+ with strict type hints |
| ML Runtime | MLX (Apple Silicon), potential GGUF/llama.cpp |
| Database | CourierDB (conversations/memory), FlowDB (workflows) |
| Auth | TBD — whatever Courier uses |
| Deployment | Local-first (macOS), potential cloud |
| Testing | pytest, httpx for API tests |
| Formatting | Black + isort + ruff |

**Adaptation:** Courier's stack is less conventional than a web app stack. The approved-stack doc should also cover **model format support** (which quantizations, which architectures) and **API compatibility targets** (OpenAI-compatible endpoints, etc.). These are the decisions that prevent "let me add support for this random model format" scope creep.

### 3F. Focus Protection → Courier

Transfers directly. Additional Courier-specific focus traps to document:

- **"Just one more model" trap** — supporting every new model release instead of making the core excellent.
- **"Feature parity" trap** — chasing feature parity with Ollama/LM Studio instead of building Courier's unique value.
- **"Premature optimization" trap** — optimizing inference speed before the API surface is stable.

### 3G. Organization → Courier

The 10-second rule and 3-level nesting transfer directly. Courier's file structure should reflect its architecture:

```
courier/
├── core/           ← Model loading, inference, memory management
├── api/            ← FastAPI routes, OpenAI-compatible endpoints
├── db/             ← CourierDB, FlowDB
├── models/         ← Model backend adapters (MLX, etc.)
└── (config, tests, scripts as needed)
```

**Adaptation:** The "naming is documentation" principle is especially important for Courier because the project spans multiple domains (ML, web APIs, databases). A file called `handler.py` means nothing. A file called `mlx_inference.py` is self-documenting.

### 3H. When Stuck → Courier

The escalation ladder transfers. Add a Courier-specific step between "timebox" and "ask for help":

`Zoom out → Break down → Timebox 25min → Check if it's a known model/framework issue (GitHub issues, Discord) → Ask for help → Walk away`

ML infrastructure has a unique failure mode: the bug is often in the model weights or the upstream library, not in your code. Checking upstream before asking for help saves time.

---

## Summary

**High-confidence transfers (use almost verbatim):**
- Decision framework (4 questions)
- Priority stack (adjust content, keep structure)
- Quality tiers (add "battle-tested" tier for inference hot path)
- Focus protection (add Courier-specific traps)
- When-stuck protocol (add upstream-check step)
- Organization philosophy (10-second rule, 3-level nesting, naming-as-docs)
- Failure philosophy

**Requires significant adaptation:**
- Coding standards (Python ecosystem, not JS)
- Approved stack (ML infrastructure, not web app)
- File naming conventions (PEP 8, not JS conventions)

**Should be written fresh (informed by patterns, not copied):**
- Identity context (Jackson's story, not Dalton's)
- Project-specific documentation
- Model serving standards (no equivalent in source material)
- API design standards (Courier's OpenAI-compatible surface)
