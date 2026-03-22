# Courier Training Page — Design Specification

> **Status:** Draft v2
> **Date:** 2026-03-22
> **Authors:** Dalton Cox, Claude (Alfred)
> **Scope:** Complete redesign of the Training experience for Courier's local AI model platform

---

## Executive Summary

The Training page is redesigned around three core principles:

1. **Training should feel like teaching, not engineering.** Users show their models what good looks like — they don't configure hyperparameters.
2. **Three distinct training modes** (Teach, Recipe, Onboard) with unique layouts, components, and emotional framing — recommended by the Courier Agent per model.
3. **Training progress unlocks Workbench tools,** creating a natural progression where users earn capabilities as they develop skills. Zero cost to Courier.

The page uses a two-level architecture: a **Model Roster** showing all models with training status and progress, and a **Model Detail** view when drilling into a specific model's training workspace.

---

## Design Principles

| Principle | What It Means |
|-----------|--------------|
| Teaching, not configuring | Users provide examples and feedback, not hyperparameters |
| Progressive disclosure | Smart defaults first, advanced controls behind a toggle |
| Show, don't tell | Live before/after comparisons instead of loss curves |
| Guardrails, not gatekeeping | System prevents common mistakes while explaining why |
| Earned complexity | Advanced tools unlock when users have the context to use them |
| Engine-agnostic | UI doesn't care if training runs on mlx-lm, Unsloth, or a cloud GPU |

---

## Research Foundation

### Competitor Analysis

| Platform | Metaphor | Strength | Weakness |
|----------|----------|----------|----------|
| Apple Create ML | Teaching / Playground | "Model as document," live preview | Apple ecosystem only |
| Google Teachable Machine | Pure classroom | Instant feedback, zero complexity | Too simple for production |
| Lobe.ai (Microsoft) | Visual studio | "Show me mistakes" UI | Discontinued |
| OpenAI Fine-tuning | Engineering dashboard | Full control | No evaluation UX, intimidating |
| HuggingFace AutoTrain | Wizard / Form-fill | Multi-model comparison | Generic, doesn't teach |
| Replicate | Recipe / Template | Intermediate visual outputs, cost transparency | Log-based progress for LLMs |

**Courier's position:** Between Create ML and Lobe — technical enough for solo devs, visual enough for business owners. The training modes system goes beyond any competitor by adapting the entire experience to the user's mental model.

### Unsloth.ai Assessment

Jackson has expressed affinity for Unsloth. Current status:
- Training requires NVIDIA CUDA GPUs (Triton kernels)
- MLX training is "coming soon" — no shipped date
- Data Recipes (dataset prep) works on all platforms
- Studio UI launched March 17, 2026 (AGPL-3.0 license)

**Recommendation:** Design the Training page to be engine-agnostic. The UI layer doesn't depend on which backend does the training. Unsloth can be integrated when their MLX support ships, or used for cloud-assisted training paths.

---

## Page Architecture

```
Training (graduation-cap icon in rail)
├── Level 1: Training Home (Model Roster)
│   ├── Active Training Banner
│   ├── Model Roster (all models, grouped by project)
│   ├── Workbench Build Meter
│   └── Recent Runs History
│
└── Level 2: Model Detail (Training Workspace)
    ├── Model Header + Progress Milestones
    ├── Agent Mode Recommendation
    ├── Training Experience (mode-specific)
    │   ├── Mode A: TEACH
    │   ├── Mode B: RECIPE
    │   └── Mode C: ONBOARD
    ├── Guardrails Dashboard
    ├── Training Progress (active run)
    └── Post-Training Summary
```

---

## Level 1: Training Home — Model Roster

A clean list of every model being trained, grouped by project. Think Linear's issue list or Vercel's project dashboard — professional, scannable, dense.

### Section 1: Active Training Banner

**Purpose:** If any model is currently training, show it at the top.

**Component: Live Training Strip**
- Model name + project + training mode
- Phase label in plain English ("Learning from your examples... epoch 4/8")
- Progress bar with time remaining
- Quality score projection ("On track for B+")
- Workbench unlock preview ("Completing this unlocks: Side-by-Side Comparison")
- Quick actions: Pause / View Details

**Behavior:** Persistent at top when any run is active. Collapses when no active runs.

---

### Section 2: Model Roster

**Purpose:** Show all models across all projects with training status at a glance.

**Component: Model Roster Row**

Each model is a row in a grouped list. Rows are grouped by project with a project header.

| Column | Content |
|--------|---------|
| Model name | e.g., "courier-chat-v3" — primary text, clickable to drill in |
| Training mode | Icon + label (Teach / Recipe / Onboard) — agent-recommended, color-coded |
| Quality grade | Letter grade with color (A=green, B=cyan, C=amber, D=red) or "—" if ungraded |
| Progress | Milestone count ("5/8") with compact progress bar |
| Improvement | Percentage improvement over base model (e.g., "+14%") |
| Status | Active (green dot) / Idle / Production Ready (checkmark) |
| Last run | Relative timestamp ("2h ago", "3 days ago") |

**Layout:** Rows grouped under project headers. Each project header shows project name and model count. Dense but readable — no cards, no grid. Rows.

**Interaction:** Click a row to enter the Model Detail view.

**Empty state:** "No models in training yet. Start by adding a model to a project and clicking New Run."

---

### Section 3: Workbench Build Meter

**Purpose:** Show cumulative workbench progress across all training activity.

**Component: Build Meter Bar**
- Progress bar: "6/14 workbench tools unlocked"
- Next unlock preview with triggering milestone
- Small icon grid of unlocked (full color) vs locked (ghosted) tools
- Users can see what's coming — locked tools are visible, not hidden

**Placement:** Below roster, above runs history. Connective tissue between Training and Workbench.

---

### Section 4: Recent Runs History

**Purpose:** Historical view of training activity across all models.

**Component: Run History Table (compact)**

| Column | Content |
|--------|---------|
| Model | Name + mode icon |
| Date | Relative timestamp |
| Duration | e.g., "2h 14m" |
| Quality | Letter grade with color |
| Result | One-line summary ("Improved 12% over base") |
| Unlock | Workbench tool earned (if any) |

---

## Level 2: Model Detail — Training Workspace

When a user clicks a model row, they enter that model's dedicated training workspace.

### Section 5: Model Header + Progress Milestones

**Purpose:** Show the model's identity, current state, and progress through training milestones.

**Component: Model Header**
- Model name, base model, project
- Training mode badge (agent-recommended, with override option)
- Quality grade (large, prominent)
- Status indicator

**Component: Milestone Bar**

A horizontal progress bar with labeled milestone markers. Not a game path — a professional progress indicator like a multi-step form or deployment pipeline.

8 milestones arranged left to right:

| # | Milestone | Trigger | Workbench Unlock |
|---|-----------|---------|-----------------|
| 1 | Data Ready | Upload 50+ examples | Data Quality Dashboard |
| 2 | First Run | Complete first training run | Model Comparison View |
| 3 | Improving | Beat base model by 10% | Prompt Template Library |
| 4 | Consistent | Score B+ on 3 consecutive runs | Batch Testing Mode |
| 5 | Edge-Tested | Pass the "Where It Struggles" review | Advanced Analytics |
| 6 | High Quality | Achieve A grade run | API Playground |
| 7 | Validated | Pass edge case evaluation suite | Production Deploy Pipeline |
| 8 | Production | Deploy with quality gate passed | Monitoring Dashboard |

**Visual:** Completed milestones are filled and labeled. Current milestone is highlighted. Future milestones are outlined/dimmed. The next workbench unlock is called out below the bar.

**Post-completion:** After reaching Production, the milestone bar stays completed and the view shifts focus to ongoing monitoring and retraining suggestions.

---

### Section 6: Agent Mode Recommendation

**Purpose:** The Courier Agent pre-selects the training mode for each model and explains why.

**Component: Mode Recommendation Card**
- Agent avatar + recommendation text
- Recommended mode as primary card (full visual treatment with preview)
- Other two modes as compact secondary pills
- Override shows a brief note explaining trade-offs

**Example:**
> "Based on how this model handles customer inquiries in QuickClaimsAI, I recommend **Teach** mode — you'll show it examples of ideal responses and it'll learn the pattern. You can switch to Recipe or Onboard if you prefer."

---

### Section 7: Training Experience (Mode-Specific)

Each mode has a distinct layout, component set, and emotional framing.

---

#### Mode A: TEACH

**Mental model:** "I'm showing my AI what good looks like."
**Best for:** Customer support bots, Q&A systems, content generation
**Emotional tone:** Patient, encouraging, coach-like

##### 7A-1: Example Pairs

**Component: Conversation Card Stack**
- Input/output pairs as chat-like cards (user message left, ideal response right)
- Per-pair: quality indicator, similarity flag, edit/delete actions
- "Add Example" button always visible
- Header: Diversity meter — "Your examples cover 5/8 topic areas"
- Conflict detection inline: "These two examples contradict each other"

##### 7A-2: Live Preview

**Component: Before/After Panel**
- Split panel: base model response (left) vs. fine-tuned response (right)
- Same prompt fed to both
- Differences highlighted subtly
- "Try another prompt" input at bottom
- Confidence indicator on fine-tuned response
- Flag if prompt matches a training example: "Try something the model hasn't seen"

##### 7A-3: Where It Struggles

**Component: Struggle Cards**
- Cards showing lowest-confidence prompts
- Each: the prompt, model's answer, expected answer
- Action: "Add better example for this" (pre-fills an example pair)
- Sorted by severity
- Expanded by default after training (can't be dismissed without reviewing)

---

#### Mode B: RECIPE

**Mental model:** "I'm adjusting ingredients and tasting the result."
**Best for:** Style tuning, tone adjustment, format control, creative output
**Emotional tone:** Experimental, playful, iterative

##### 7B-1: Ingredient Panel

**Component: Recipe Slider Board**
- Plain-English sliders with live preview snippets:
  - "How creative vs. precise?" (maps to temperature)
  - "How long should responses be?" (maps to max tokens)
  - "How closely should it follow instructions?" (instruction adherence)
  - "How formal should it sound?" (style/tone)
- Each slider shows preview text at current position
- "Smart Defaults" reset button

##### 7B-2: Taste Test

**Component: A/B Comparison Cards**
- Side-by-side outputs: current settings vs. previous settings
- "Which is better?" — user picks, system learns preference
- Checkpoint selector: compare any two training checkpoints
- Optional star rating per output

##### 7B-3: Recipe Card

**Component: Saved Configuration Card**
- Current recipe as saveable/shareable card
- All slider positions + settings in clean format
- Version history: "Recipe v1, v2, v3" with one-tap comparison
- "Apply to another model" option

---

#### Mode C: ONBOARD

**Mental model:** "I'm bringing a new team member up to speed."
**Best for:** Code assistants, domain experts, RAG specialists
**Emotional tone:** Structured, professional, thorough

##### 7C-1: Playbook Upload

**Component: Document Intake Panel**
- Drag-and-drop for documents (PDF, DOCX, CSV, TXT)
- Auto-parsing with preview: "Found 127 Q&A pairs in this document"
- Document health: formatting issues, duplicates, quality score
- Section breakdown: "Chapter 1: 23 examples, Chapter 2: 45 examples"
- "Highlight Key Sections" — user marks priority areas

##### 7C-2: Competency Checklist

**Component: Skills Assessment Board**
- Grid of competency areas auto-detected from training data
- Per competency: name, # training examples, current score, status
- Status levels: Learning (red) → Competent (amber) → Expert (green)
- "Add training data for [weak area]" action on red/amber cells
- Overall: "Ready for 7/10 competency areas"

##### 7C-3: Review Meeting

**Component: Evaluation Interview Panel**
- Structured test scenarios walked through with the model
- Per scenario: situation prompt, model response, user rates pass/fail
- Failed scenarios become training priorities
- "Schedule re-review" reminder
- Summary: "Passed 8/10 scenarios. Focus areas: [edge cases]"

---

### Section 8: Guardrails Dashboard

**Purpose:** Prevent common training mistakes without lecturing.

**Component: Data Health Card**
- Always visible, compact by default, expandable
- Traffic light indicators per category

| Guardrail | What It Checks | Visual |
|-----------|---------------|--------|
| Volume | Minimum example count (floor: 10, warning: 10-50, good: 50+) | Green / Amber / Red |
| Diversity | Semantic similarity analysis across examples | Green / Amber / Red |
| Consistency | Contradiction detection between examples | Green / Amber / Red |
| Formatting | HTML artifacts, encoding issues, length variance | Green / Amber / Red |
| Source Quality | AI-generated content flagging, spot-check prompt | Green / Amber / Red |

**System-enforced guardrails (non-UI):**

| Guardrail | Behavior |
|-----------|----------|
| Auto eval holdback | 15% of data reserved for testing, non-negotiable |
| Auto early stopping | Training pauses when overfitting detected, best checkpoint saved |
| Training-data test flag | Warning when user tests with prompts matching training examples |
| Loss spike normalization | "This is normal" message when loss increases mid-training |
| Evaluation gate | Mandatory 3-5 prompt review before model can be set as active |

---

### Section 9: Training Progress (Active Run)

**Component: Training Progress Panel**

| Element | Description |
|---------|------------|
| Phase labels | "Preparing → Learning → Evaluating → Complete" (plain English) |
| Progress bar | With time remaining |
| Quality projection | Updating in real-time ("On track for B+") |
| Checkpoint samples | Periodic before/after comparisons as model trains |
| Fluctuation normalizer | "Performance dip is normal — reorganizing knowledge" |
| Workbench countdown | "Completing this run unlocks Batch Testing" |
| Milestone progress | Current milestone indicator |

---

### Section 10: Post-Training Summary

**Component: Run Report Card**

| Element | Description |
|---------|------------|
| Quality grade | Letter grade, prominently displayed |
| Plain-English summary | "Improved 14% over base model on held-back test data" |
| Strengths | What the model does well now (bulleted) |
| Growth areas | What to train on next (bulleted, actionable) |
| Workbench unlock | Tool earned, with "Go to Workbench" action |
| Milestone cleared | Confirmation + next milestone preview |
| Agent suggestion | Recommended next action |

---

## Progression System: Training → Workbench

Training progress unlocks Workbench tools. Users start with a basic workbench and build it up through training milestones. Each unlock arrives when the user has the context to use it.

### Unlock Sequence

| Milestone | Workbench Unlock | Why At This Stage |
|-----------|-----------------|-------------------|
| Data Ready | Data Quality Dashboard | User is working with data, needs quality tools |
| First Run | Model Comparison View | User now has two things to compare (base vs. fine-tuned) |
| Improving | Prompt Template Library | User understands input patterns, ready for templates |
| Consistent | Batch Testing Mode | User has reliable model, ready to test at scale |
| Edge-Tested | Advanced Analytics | User has seen struggles, ready for deeper metrics |
| High Quality | API Playground with Auth | User is deploying, needs programmatic access |
| Validated | Production Deploy Pipeline | User has proven quality, ready for production |
| Production | Monitoring Dashboard | Model is live, needs ongoing observation |

### Design Rules

- **Locked tools are visible, not hidden.** Users see what's coming and what earns it.
- **Unlocks feel earned, not arbitrary.** Each tool connects to the training milestone that makes it relevant.
- **No artificial gates.** Every lock has a clear, achievable key.
- **Unlocks are permanent.** Once earned, never revoked.

---

## Engagement & Rewards (Phased)

### V1 (Launch)
- Milestone progression (professional, not gamified)
- Quality grades (A/B/C/D) on every run
- Workbench unlocks tied to training milestones
- "Beat the Base" improvement percentage
- Agent-recommended next actions

### V2 (With User Data)
- Training rewards: quality runs earn platform perks (feature unlocks, extended compute, pro model trial access)
- Perk tiers scaled to training quality — must meet guardrail standards to qualify
- Conversion funnel: earned perks introduce premium features users then want to keep

### V3 (With Scale)
- Partner-funded rewards (model providers supply API credits for training activity on their models)
- Community showcase for production-ready models
- Cross-model progression bonuses

### Never
- Points, XP, or leveling systems
- Leaderboards
- World maps, campaigns, or game-themed UI
- Participation trophy badges
- Animations that slow the user down
- Notifications that feel like a mobile game
- Artificial gates ("train 5 times to unlock Recipe mode")
- Streak mechanics that punish missed days

---

## Technical Notes

### Engine Agnosticism

The Training page UI is decoupled from the training backend. The interface communicates through an abstraction layer that supports:

- **mlx-lm** — Apple's MLX fine-tuning (local, Apple Silicon native)
- **Unsloth** — When MLX backend ships, or for cloud-assisted training
- **Cloud GPU** — Hetzner/Lambda with GGUF pull-back for hybrid workflows

The UI only needs: start/stop/pause, progress updates, checkpoint data, and evaluation results. It doesn't care which engine provides them.

### Data Format

Accept multiple input formats with automatic conversion:
- JSONL (ChatML / Alpaca / ShareGPT formats)
- CSV with column mapping
- PDF / DOCX / TXT (via document parser, especially for Onboard mode)
- Conversation exports from existing chat tools

### Quality Score Calculation

The letter grade is a composite of:
- **Data quality** (guardrails pass rate) — 25%
- **Training stability** (no overfitting, clean convergence) — 25%
- **Improvement over base** (eval holdback performance delta) — 30%
- **Evaluation coverage** (% of competency areas tested) — 20%

Thresholds: A (90+), B (75-89), C (60-74), D (below 60)

---

## Open Questions

1. **Milestone customization:** Should power users be able to add custom milestones, or is the standard 8-step sequence fixed?
2. **Cross-model progression:** Does training Model A contribute to workbench unlocks that benefit Model B, or is each model independent?
3. **Team features:** If Courier adds multi-user support, do training milestones belong to the user or the project?
4. **Offline resilience:** How does the progression system work if the user is fully offline?
5. **Mode switching mid-progress:** Can a user change from Teach to Recipe mode mid-training, or does it reset milestone context?

---

## Next Steps

1. Design the Workbench page spec (the core operational experience that training feeds into)
2. Define the complete page inventory across all of Courier
3. Build in code — real Next.js components with real Tailwind, not wireframes
