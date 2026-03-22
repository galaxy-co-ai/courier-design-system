# Courier Product Design Brief

## From a Power User to a Founder

---

### Executive Summary

Courier's backend infrastructure is genuinely best-in-class — zero downtime, zero failures, seamless multi-model deployment across real production projects. The gap isn't in what Courier *does*, it's in how Courier *communicates* what it's doing. Users think in projects; Courier thinks in models. Closing that gap — and layering in an agent-first experience — transforms Courier from a model server into a private AI operations platform.

---

### The Current State

Credit where it's due: **the engine is flawless.**

Courier's backend has handled every production workload thrown at it without a single failure. Multiple models running simultaneously across multiple projects, zero downtime, zero data loss, zero "why did this break at 2am" moments. That's not table stakes — that's rare. Most AI infrastructure at this stage is held together with duct tape and optimism. Courier's foundation is genuinely solid.

The deployment model is elegant in its simplicity: local-first, no cloud dependency, no API cost anxiety. For users running M-series Macs, it feels like having a private model factory with almost no setup burden. The MCP server integration with Claude Code was a smart move — it meets developers where they already work instead of forcing them into a separate tool.

Jackson has built something that *works*. The question isn't whether the product is real — it is. The question is whether the experience around it matches the quality of what's underneath.

---

### The Gap: Model-Centric vs. Project-Centric

Here's the fundamental UX problem: **Courier's mental model doesn't match its users' mental model.**

A developer doesn't wake up thinking "I need to manage my Mistral 7B instance." They wake up thinking "I need to ship QuickClaims" or "I need to get the CB Media pipeline working." Projects are the unit of work. Models are tools *inside* that unit. But Courier's entire interface — navigation, analytics, dashboard — is organized around models, not projects.

The consequences show up in daily usage:

**The dashboard isn't sticky.** It gets opened when adding a model to a project, then closed. There's no reason to keep it open because it doesn't reflect the state of work in progress.

> *"Otherwise it doesn't do me any good to have it open."*

**Project-to-model mapping is invisible.** After a month of production use across multiple projects, there's no way to answer the basic question: "Which models are powering which projects right now?"

> *"No shit I have no clue lol. I really do not have a way to track it visually."*

**The workbench concept is unclear.** On first load, the dashboard shows an empty workbench with a full model grid below it. Is a workbench a group for one project? A set of active models across all projects? A favorites list? The metaphor doesn't land.

**Model selection is still a guess.** Model cards show technical specs — parameter counts, quantization levels, context windows. But they don't answer the question users actually have: "Which model should I use for *this* task in *this* project?" That answer still requires asking Jackson directly.

> *"Probably explains why I still don't know which model to use for what."*

The clearest articulation of the gap came in a single comparison:

> *"It feels like I got the keys to a local model factory I can use anytime, with almost no setup burden, so simple."*

What it should feel like:

> *"I got the keys to the local model factory, AND I had my own private operations building that's set up specifically for me and my projects."*

That's not a complaint. That's a product vision.

---

### The Agent Vision

This is the most important insight from the entire interview, and it deserves its own section because it reframes what Courier *is.*

Dalton isn't asking for a better dashboard. He's not asking for more charts or a redesigned sidebar. He's asking for **an autonomous AI partner that lives inside Courier** — one that understands his projects, recommends models, builds training plans, and communicates in natural language.

**Why this matters:** The current workflow requires the user to understand model architectures, know which models fit which tasks, manually connect models to projects, and independently figure out training pipelines. That workflow scales to maybe a dozen technically sophisticated early adopters. The agent vision scales to *anyone who runs a business.*

Here's what the agent experience looks like:

**Repo inspection and project sheets.** Point the Courier agent at a GitHub repo. The agent inspects the codebase, asks clarifying questions about the project's goals, then generates a "project sheet" — a Courier-specific analysis of where local models could add value. Not a generic AI audit, but targeted recommendations: "Your claims intake parser would benefit from a fine-tuned document extraction specialist. Your customer-facing chat could run on a smaller, faster model. Here's a training plan for each."

**Natural language training.** This is the unlock that expands Courier's audience beyond developers. Instead of requiring users to understand fine-tuning parameters, LoRA adapters, and dataset formatting, the agent handles the translation: "Upload your insurance claim documents. I'll prepare the training data, select the right base model, and start the fine-tuning process. You'll see progress in real-time."

**Per-project specialists, not monolithic models.** The mental model isn't "one fine-tuned model per project." It's multiple specialists per project — a customer service agent, a sales agent, a document parser, a content pipeline operator — each trained on different data, each optimized for a different task. The Courier agent orchestrates all of them.

**The "operations building" metaphor.** Instead of flat pages (Models, Analytics, Docs), the interface becomes a set of purpose-built rooms:

- A room for **fine-tuning and training** — per-project, with progress tracking
- A room for the **Courier agent** — repo inspection, recommendations, project sheets
- A room for **external connections** — MCP status, Claude Code integration health, gap analysis
- A room for **live operations** — the command center view with real-time data

This isn't feature creep. It's a coherent product vision that emerges directly from trying to use Courier to build real things.

---

### User Stories

Based on direct interview responses:

1. **As a developer managing multiple projects,** I want to see which models are connected to which projects at a glance, so that I don't have to remember (or guess) my own infrastructure.

2. **As a non-technical business owner,** I want to describe what I need in plain language and have Courier recommend the right model, so that I don't need to understand model architectures to benefit from local AI.

3. **As a power user,** I want the dashboard to function as a real-time command center, so that I have a reason to keep it open during my workday.

4. **As a developer starting a new project,** I want to point Courier at my GitHub repo and receive a project sheet with model recommendations, so that I can integrate local AI from day one instead of figuring it out later.

5. **As a user training a model,** I want to see real-time progress with pass/fail status (like a Vercel deployment log), so that I know whether training is working without checking back manually.

6. **As a project lead,** I want to train multiple specialist models per project (customer service, document parsing, sales), so that each AI function is optimized for its specific task.

7. **As a user with documents and domain data,** I want to upload files and have Courier handle the entire training pipeline, so that I can fine-tune models without understanding the technical mechanics.

8. **As a developer using Claude Code,** I want to see MCP connection status and identify integration gaps directly in the Courier dashboard, so that I know which tools are connected and which need attention.

9. **As a user evaluating models,** I want model cards that show use-case categories and "best for" tags instead of only technical specs, so that I can choose the right model for my task without external research.

10. **As a team lead considering Courier for my department,** I want to see per-project cost savings and token usage, so that I can quantify the ROI of running local models versus cloud APIs.

---

### Competitive Landscape

No one has built what Courier could become. Each of these platforms owns a piece of the puzzle — none of them own the whole picture.

**Vercel**
*What they do well:* Project-first navigation. Every interaction starts with "which project?" not "which technology?" The deployment log is a masterclass in progress UI — real-time, visual, pass/fail clarity that makes you *want* to watch it. The activity feed creates a sense of momentum.
*What they're missing:* Vercel is a deployment platform, not an AI platform. No model management, no training, no agent layer. But their UX patterns are the gold standard for developer tools.
*Courier's opportunity:* Adopt the project-first mental model and the deployment-log-as-progress-UI pattern directly. Dalton cited Vercel by name as the experience he wants for model training.

**OpenRouter**
*What they do well:* Model comparison with real cost transparency. Benchmarks per model. The ability to see "Model A costs $X per million tokens and scores Y on this benchmark" side by side. Makes model selection feel informed rather than arbitrary.
*What they're missing:* It's a routing layer, not a platform. No project context, no training, no agent. You pick a model and send it elsewhere. There's no persistent relationship with your work.
*Courier's opportunity:* Bring cost comparison and benchmark data into model cards, but contextualize it: "For *your* project's typical workload, Model A would cost X and perform Y." Local-first economics make Courier's cost story even stronger.

**Replicate**
*What they do well:* Humanized fine-tuning. Drag-and-drop data upload, no-code training interface. They've made model training feel approachable instead of intimidating.
*What they're missing:* Cloud-dependent, usage-based pricing that scales unpredictably. No project-level organization. No agent to guide the process. Training is accessible but still requires the user to know *what* to train and *why*.
*Courier's opportunity:* Match the accessibility of Replicate's training UX, but local-first (no cloud costs) and agent-guided (Courier recommends what to train, not just how).

**Unsloth**
*What they do well:* Local fine-tuning with a no-code web UI. Side-by-side model comparison before and after training. Data pipeline from documents to training datasets. They've proven that local fine-tuning can be approachable.
*What they're missing:* Developer-focused, not business-user-friendly. No project context. No agent layer. The UI is functional but not engaging. It's a tool, not a platform.
*Courier's opportunity:* Unsloth proves the technical feasibility of local fine-tuning with good UX. Courier can go further by adding project context, agent guidance, and a platform experience that makes training feel like a natural part of project development.

**Hugging Face**
*What they do well:* Model cards as a discovery experience. Community and ecosystem. Spaces for live demos. The model card format — description, use cases, benchmarks, example outputs — has become the standard for understanding what a model can do.
*What they're missing:* It's a marketplace, not a workspace. You discover models on Hugging Face, then leave to use them. No project management, no training orchestration, no agent. The community is a strength and a distraction simultaneously.
*Courier's opportunity:* Adopt the model card format but make it actionable: "This model is good for X. You have a project that needs X. Connect them." Discovery that leads directly to deployment, without leaving the platform.

**OpenSea**
*What they do well:* Data-dense, full-viewport dashboards that feel alive. Real-time activity, trending metrics, collection-level views that reward exploration. The aesthetic is engaging without being cluttered — it makes you want to scroll.
*What they're missing:* Completely different domain. But the UX pattern of "data as entertainment" is transferable.
*Courier's opportunity:* Dalton cited OpenSea by name as the aesthetic he wants. The principle: dashboards should feel like live environments, not static report pages. Activity feeds, real-time metrics, visual density that rewards engagement.

**The synthesis:** Vercel's project-first architecture + OpenRouter's model comparison + Replicate's accessible training + Unsloth's local-first fine-tuning + Hugging Face's model discovery + OpenSea's data density = Courier's opportunity. No one has combined all of these into a single local-first platform with an agent layer. That's the whitespace.

---

### Proposed Information Architecture

Based on the "operations building" metaphor, reorganized around projects as the primary object:

**Projects** *(NEW — primary navigation object)*
The home base. Each project shows:
- Connected models and their roles (which model handles what)
- Per-project analytics (tokens, latency, cost savings vs. cloud)
- Agent recommendations ("Based on usage patterns, consider adding a document specialist")
- Training status for any active fine-tuning jobs
- External connections (MCP, Claude Code, other integrations)

**Models** *(existing — now secondary, accessed through projects or standalone)*
Model detail shows:
- Which projects use this model (reverse mapping)
- Performance data across projects
- "Best for" tags and use-case categories
- Comparison view against similar models
- One-click deploy to a project

**Training** *(NEW)*
The fine-tuning workshop:
- Natural language interface: describe what you need, Courier recommends the approach
- Document/data upload with automatic dataset preparation
- Base model selection with guided recommendations
- Real-time training progress (Vercel-style deployment log with pass/fail)
- Before/after comparison on sample inputs
- Training history and versioning

**Agent** *(NEW)*
The Courier agent's home:
- Chat interface for natural language interaction
- Repo inspection: paste a GitHub URL, agent analyzes and builds a project sheet
- Model recommendations based on project analysis
- Training plan generation
- Connection status board: MCP servers, Claude Code, external tools — what's connected, what's missing, what could be added

**Analytics** *(existing — enhanced)*
The command center:
- Real-time data (not batch-updated)
- Per-project filtering (not just per-model)
- Cost savings calculator (local vs. cloud API equivalent)
- Activity feed with status indicators
- Full-viewport, data-dense layout (the OpenSea/statcast aesthetic)

**Playground** *(new or enhanced)*
The testing ground:
- Side-by-side model comparison on your own prompts
- Test against your project's actual use cases, not generic benchmarks
- Compare base model vs. fine-tuned variant
- Share results with team members

---

### Design Patterns to Adopt

Specific UI patterns drawn from competitive research and direct interview feedback:

- **Project-to-model connection visualization.** A clear, visual map showing which models serve which projects. Think dependency graphs, not spreadsheets. This solves the "I have no clue which project uses what" problem directly.

- **Vercel-style deployment/training log.** Real-time status feed with pass/fail indicators for model training and deployment. Activity log with build status. Minimal notification icon for status updates per run. Dalton described this exact pattern by name.

- **Model cards with "best for" tags.** Move beyond parameter counts and quantization levels. Add use-case categories: "Best for: document parsing, structured extraction, low-latency chat." Solve the "which model for what?" problem at the card level.

- **Agent chat interface within the dashboard.** Not a separate app. Not a browser extension. A persistent chat surface inside Courier where users interact with their agent in natural language. The agent is a first-class citizen of the platform, not a bolt-on.

- **Data-dense, full-viewport dashboard.** The command center should feel alive — real-time metrics, activity feeds, status indicators, trending data. OpenSea and live sports statcast visuals were cited as the aesthetic target. Static report pages with wide margins are the anti-pattern.

- **Recommendation engine UI.** "Based on your QuickClaims usage patterns, consider training a specialist for claims intake parsing. Here's a one-click training plan." Proactive, contextual, actionable.

- **Per-project cost and token tracking.** Show the economic value of running local. "This project used X tokens this month. Cloud equivalent: $Y. You saved $Z." Make the ROI tangible and continuous.

- **MCP connection status board.** Which tools are connected to Courier, which have active sessions, which have gaps. A health dashboard for the integration layer. Developers shouldn't have to guess whether their Claude Code ↔ Courier connection is working.

---

### What This Is Not

This brief is a direction, not a spec. It's a user's perspective, not an engineer's mandate.

Jackson knows his product better than anyone. He knows the technical constraints, the infrastructure costs, the roadmap tradeoffs that don't show up in a user interview. This document doesn't pretend to know those things.

What it offers is a lens he might not have — the lens of someone who has been trying to use Courier to build real production software for a month, who ran into the same friction points repeatedly, and who came away believing Courier's ceiling is significantly higher than its current floor.

The backend is already exceptional. The agent vision is ambitious but grounded in real usage. The project-first reorganization is the kind of structural change that's easier to make now than later.

None of this needs to ship tomorrow. But if it informs where Courier goes next, this brief did its job.

---

### A Note on the Audience

The most significant insight might have come in the final answer of the interview.

When asked who he'd recommend Courier to, the response wasn't limited to developers:

> *"Any business owner or department lead. It would completely unlock the ability to integrate AI into their company without having to set it all up themselves, cut AI API costs by over 50%."*

And on the training vision:

> *"If it could train models through natural language, none of their internal employees have to be AI experts. It's built to empower, not replace."*

That last line is a positioning statement, and it's a good one. "Built to empower, not replace."

If Courier's training interface becomes accessible through natural language — if a marketing director can upload brand guidelines and train a content specialist without understanding LoRA adapters — the total addressable market shifts from "developers with M-series Macs" to "any team that wants private AI without the expertise tax."

That's a fundamentally different company. And the backend to support it already exists.

---

### Credits

Product design brief based on an extended interview with **Dalton Cox**, Co-founder & Tech Lead at [GalaxyCo.ai](https://galaxyco.ai) — one of Courier's first four test users, running Courier in production across QuickClaimsAI, Results Roofing, and the GalaxyCo ecosystem.

The best feedback comes from people who care enough to be honest.
