# Courier Engineering Constitution

> The rules that keep Courier's product sharp, its codebase honest, and its founder sane.

---

## I. Design Philosophy

Courier is infrastructure. Its UI serves operators who monitor inference, manage models, and debug failures under pressure. Every design decision flows from that reality.

**Core principles:**

- **Data-dense by default.** Show more, scroll less. Operators need model status, throughput, latency, memory pressure, and queue depth visible simultaneously. Dashboards that require clicking through to find critical info have failed.
- **Dark-first, always.** Slate/charcoal backgrounds (`--background: slate-950`, `--surface: slate-900`). Cyan (`--primary`) for interactive elements and status-positive. Amber (`--secondary`) for warnings and attention. This isn't aesthetic preference — it's reduced eye strain during long inference monitoring sessions.
- **Border-based hierarchy, not shadows.** On dark backgrounds, box-shadows are invisible or muddy. Use surface luminance stepping (`slate-950 → slate-900 → slate-800`) and `1px` borders (`slate-700/50`) to create depth. Reserve glow effects for active/focus states only.
- **Every pixel earns its space.** If a UI element doesn't help the operator make a decision or take an action, remove it. No decorative illustrations. No "welcome back!" banners. No empty states with cartoon mascots. A model list with zero models should say "No models loaded" and show the load command.
- **Technical language is a feature.** Users know what quantization, context length, tokens/sec, and VRAM mean. Don't dumb it down. Don't add tooltips explaining "GPU" to people running MLX on M-series chips.
- **Typography serves hierarchy.** HubotSans Black for headings — bold, distinctive, engineered. GeneralSans for body — clean, readable at small sizes. Two fonts, no exceptions. Size scale: `text-xs` for metadata, `text-sm` for body, `text-base` for emphasis, `text-lg`+ for section headers only.

### Anti-Patterns (Never Do These)

| Anti-Pattern | Why It Fails |
|---|---|
| Shadows for hierarchy on dark backgrounds | Invisible. Use surface luminance + borders instead |
| Animating high-frequency interactions (tab switches, list refreshes, status updates) | Feels sluggish at 10+ updates/sec. Reserve motion for user-initiated transitions only |
| Softening technical language ("your smart assistant" instead of "7B Q4_K_M") | Patronizes the audience. They chose Courier because they're technical |
| `rounded-full` / `rounded-xl` on everything | Infrastructure dashboards need sharp precision. Use `rounded-md` for cards, `rounded-sm` for inputs, sharp corners for tables and code blocks |
| Marketing copy in the product chrome | "Supercharge your AI workflow!" has no place in a model management panel |
| Hiding configuration behind "simple mode" | Courier users want the knobs. Surface config directly with smart defaults |
| Rainbow chart palettes | Use a perceptually uniform palette. Cyan→blue→violet for sequential data. Categorical: max 6 distinct hues, all passing WCAG on dark backgrounds |

---

## II. Code Standards

### Frontend (Next.js 16 / React 19 / TypeScript)

```
- Strict TypeScript. No `any`. No `as` casts unless genuinely unavoidable (document why).
- Server Components by default. Client Components only for interactivity (state, effects, events).
- Tailwind v4 utility classes. No inline styles. No CSS modules unless fighting specificity.
- shadcn/ui as component base — extend, don't wrap. Customize via CSS variables, not prop drilling.
- File naming: `kebab-case.tsx` for components, `camelCase.ts` for utilities.
- Colocation: component + its types + its tests in the same directory.
- Imports: absolute paths via `@/` alias. Group: react → next → third-party → internal.
- No barrel files (`index.ts` re-exports). Direct imports only.
```

### Backend (Python / Uvicorn / MLX)

```
- Type hints everywhere. Use `typing` module for complex types.
- Async by default. Uvicorn is async — don't block the event loop.
- Error responses include context: what failed, why, what the caller can do about it.
- No commented-out code in main branch. Use git history.
- TODOs include context: `# TODO(jackson): Rate limiting — need before public beta`
- Docstrings on public functions. Skip on obvious private helpers.
- CourierDB/FlowDB access through defined interfaces, not raw queries scattered in routes.
```

### Shared

- **Comments explain WHY, not WHAT.** `# Retry 3x because MLX model loading is flaky on cold start` is useful. `# Load the model` is noise.
- **Error handling has context.** Catch specific exceptions. Include the model name, the operation, the state. "Failed to load model" is useless. "Failed to load mistral-7b-q4: VRAM insufficient (needs 4.2GB, available 3.1GB)" is actionable.
- **No magic numbers.** Extract to named constants. `MAX_CONCURRENT_INFERENCES = 4` not `if len(queue) > 4`.

---

## III. Quality Gates

Every change — feature, fix, or refactor — passes through these checks before merge.

### Visual Checklist
- [ ] Dark theme tested (primary context — test this first, not light mode)
- [ ] Text passes WCAG AA contrast on `slate-900`/`slate-950` backgrounds
- [ ] No layout shift on data load (skeleton or fixed-height containers)
- [ ] Responsive at 1280px (primary), 1024px (supported), 768px (functional)
- [ ] Monospace font used for all numeric/code values (model names, stats, IDs)
- [ ] Cyan accent used consistently for primary actions and positive status
- [ ] Amber accent used only for warnings and attention-requiring states
- [ ] No orphaned hover states (if it looks clickable, it is clickable)

### Technical Checklist
- [ ] TypeScript compiles with zero errors and zero `any` suppressions
- [ ] No `console.log` in committed code (use structured logging)
- [ ] API responses typed end-to-end (backend schema → frontend type)
- [ ] Loading states exist for every async operation
- [ ] Error states exist and are tested (not just happy path)
- [ ] No N+1 queries against CourierDB/FlowDB

### Accessibility Baseline
- [ ] All interactive elements keyboard-reachable
- [ ] Focus indicators visible on dark backgrounds (cyan ring, not default blue)
- [ ] Screen reader labels on icon-only buttons
- [ ] Status changes announced via `aria-live` regions
- [ ] Color is never the sole indicator (pair with icon or text)

---

## IV. Decision Framework

When facing a UI/UX decision, run through these four questions in order:

### 1. Does this align with what Courier is?
Courier is a local-first, performance-obsessed inference platform for technical users. If a feature or design choice moves toward "friendly consumer app," it's wrong. If it moves toward "powerful operator tool," it's right.

### 2. What's the cost of reversing this?
- **Low cost** (CSS change, copy update, component variant): Ship it, learn from real usage.
- **Medium cost** (new component pattern, API shape, data model): Prototype first, get a second opinion.
- **High cost** (architecture change, database schema, auth model): Write it down, sleep on it, decide tomorrow.

### 3. Will future-me thank present-me?
Technical debt compounds. But premature abstraction wastes time on problems that don't exist yet. The sweet spot: write clean code for today's requirements, leave obvious extension points, don't build the framework.

### 4. Am I building this because users need it, or because it's interesting?
Bootstrapped means every hour counts. The "just one more model format" trap, the "feature parity with Ollama" trap, the "premature optimization of cold start times" trap — they all feel productive but don't ship revenue.

### Priority Stack (when everything feels urgent)
1. **Stability** — inference reliability, no crashes, no data loss
2. **Core UX** — model loading, API serving, monitoring
3. **Polish** — transitions, empty states, error messages
4. **New features** — only after 1-3 are solid
5. **Exploration** — new model formats, experimental APIs

### When Stuck
1. Zoom out — am I solving the right problem?
2. Break it down — what's the smallest next step?
3. Timebox — 30 minutes, then reassess approach
4. Check upstream — did MLX/Next.js/Python change something?
5. Ask for help — Claude, community, docs
6. Walk away — seriously, 20 minutes outside fixes more bugs than 2 hours of staring

---

## V. Working With AI (Claude Code)

### Getting Good Output
- **Start with context.** "I'm working on the model management page. Here's the current component. I need to add batch delete." beats "Add a delete feature."
- **Share the file.** Don't describe code — paste it or point to the path. AI hallucinates less with real code in context.
- **State constraints upfront.** "Must work with our existing CourierDB interface" or "No new dependencies" saves a round-trip.
- **One task per prompt.** "Refactor this AND add tests AND update the docs" gets mediocre results on all three. Sequence them.

### What AI Is Good At (Lean Into)
- Generating TypeScript types from API response shapes
- Writing test cases (especially edge cases you'd skip)
- Refactoring repetitive code into patterns
- Drafting error messages with context
- Catching accessibility gaps
- CSS/Tailwind translation (design → code)

### What AI Is Bad At (Stay Hands-On)
- Understanding your deployment topology (local MLX + Vercel hybrid)
- Making product priority calls (that's founder judgment)
- Knowing what your users actually complain about
- Performance optimization without profiling data
- Naming things (always rename AI suggestions to match your conventions)

### The Golden Rule
AI is a multiplier, not a replacement. It makes a good engineer faster. It doesn't make a non-engineer good. You still own every line that ships.

---

*Built for Courier. Built for Jackson. Ship fast, ship sharp, ship local.*
