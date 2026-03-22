# Courier Product Profile

> **Purpose:** Canonical reference for all downstream design system work. Every token, component, and layout decision traces back to this document.

---

## 1. Product Positioning

### What It Is

Courier is a local AI model serving platform purpose-built for Apple Silicon. It transforms M-series Macs into production-ready AI API platforms — no cloud dependency, no per-token billing, no data leaving the machine.

### Who It's For

- **ML engineers and AI developers** who want OpenAI-compatible API endpoints running locally
- **Privacy-conscious teams** that need inference without data egress
- **Indie hackers and bootstrapped builders** optimizing for cost (zero marginal inference cost after hardware)
- **Apple ecosystem power users** who want to leverage M-series unified memory for model serving

### Value Proposition

| Cloud AI APIs | Courier |
|---|---|
| Per-token pricing | Zero marginal cost after hardware |
| Data leaves your machine | 100% local inference |
| Vendor lock-in (API format) | OpenAI-compatible + custom API |
| Rate limits | Hardware-bound only |
| Requires internet | Fully offline-capable |
| Black-box infrastructure | Full visibility via analytics dashboard |

**Core promise:** Production-grade AI serving with the economics and privacy of local compute.

### Builder Context

- Built by **Jackson** at **RecursionAI** (https://courier.thinkrecursion.ai/)
- 100% bootstrapped — no VC, no enterprise sales motion
- Product DNA: developer tool, not enterprise platform. Speed and pragmatism over ceremony.

---

## 2. Brand Voice & Personality

### Inferred Voice Attributes

| Attribute | Evidence |
|---|---|
| **Technical confidence** | Exposes quantization configs, model parameters, API specs directly in the UI — no dumbing down |
| **Minimal, not cold** | Dark UI with selective color accents; information-dense but not cluttered |
| **Builder-to-builder** | No marketing fluff in the product chrome — metrics, status, controls front and center |
| **Quietly ambitious** | "Turns your Mac into a production API platform" is a big claim delivered matter-of-factly |
| **Utilitarian luxury** | The aesthetic is premium (dark + cyan + precise typography) but every element earns its pixels with function |

### Tone Guidelines (for design system copy)

- **Direct over clever.** Say what it does, not what it's like.
- **Technical vocabulary is fine.** Users know what quantization, latency, and throughput mean.
- **Confidence without hype.** "Production-ready" is stated as fact, not aspiration.
- **Terse labels.** UI chrome uses uppercase tracked text for categories (e.g., "STATIC", "FLEX", "COURIER CLOUD", "SHARED"). Economy of words.

---

## 3. Visual Identity Summary

### Color System

Courier uses a **dark-first** palette with OKLCH/Lab color values (Tailwind CSS v4 native). CSS class `dark` is set on the `<html>` element.

#### Semantic Token Map

| Token | Role | Observed Value Range |
|---|---|---|
| `--background` | Page/app background | Near-black, deep slate (~`oklch(0.14 0.005 260)`) |
| `--foreground` | Primary text | High-contrast white/near-white |
| `--card` | Card surfaces | Slightly elevated from background (1-2% lighter) |
| `--card-foreground` | Card text | Matches `--foreground` |
| `--primary` | Primary accent | **Cyan/teal** — the signature color. Used for active states, chart accents, primary actions |
| `--secondary` | Secondary surfaces | Muted dark surfaces for hover/selected states |
| `--muted` | Subdued backgrounds | Darker than card, used for subtle differentiation |
| `--muted-foreground` | Secondary text | Gray, ~50-60% opacity against background |
| `--accent` | Highlight accent | **Amber/orange** — used sparingly for badges ("COURIER CLOUD", "SHARED"), warnings |
| `--destructive` | Error/danger | Red family |
| `--border` | Borders | Subtle, low-contrast against card backgrounds. Border-driven hierarchy (not shadow) |
| `--ring` | Focus rings | Matches primary (cyan) |
| `--popover` | Popover surfaces | Matches or slightly lighter than card |
| `--sidebar` | Sidebar background | Darker than main content area |
| `--sidebar-foreground` | Sidebar text | |
| `--sidebar-primary` | Sidebar active item | Cyan accent |
| `--sidebar-accent` | Sidebar hover/selected | Muted highlight |
| `--chart-1` through `--chart-5` | Data visualization | Cyan primary, with supporting palette for multi-series |
| `--radius` | Border radius | Moderate — rounded but not pill-shaped |

#### Key Color Observations

- **Hierarchy is built with background layers + borders**, not drop shadows. Cards sit on backgrounds via 1-2% luminance difference and a subtle border.
- **Cyan/teal is the brand signature.** It carries all primary actions, active states, chart fills, and focus indicators.
- **Amber/orange is purely informational.** Badges, status indicators, secondary categorization. Never used for actions.
- **No light mode observed.** Design system should treat dark as the canonical theme. Light mode, if added later, would be a derivative.

### Typography

| Role | Font | Weight | Details |
|---|---|---|---|
| **Body / UI** | GeneralSans | Variable (400-600 typical) | Self-hosted WOFF2, loaded via `next/font`. Clean geometric sans. |
| **Headings / Display** | HubotSans | Black (900) | Self-hosted WOFF2, loaded via `next/font`. High-impact, dense weight for headlines and hero text. |
| **Monospace** | System mono stack (assumed) | Regular | For code blocks, API examples, model IDs |

#### Typography Patterns

- **Metric cards:** Large numerical values (likely HubotSans or GeneralSans semibold), uppercase tracked labels above (GeneralSans, small, `letter-spacing: 0.05-0.1em`, muted foreground), muted subtext below.
- **Section headers:** Moderate size, GeneralSans medium/semibold. Not oversized — the UI is information-dense.
- **Card titles:** Model names in medium weight, config details (quantization, mode) in smaller muted text or badges.
- **Navigation:** GeneralSans regular/medium, icon-paired, moderate size.

### Spacing & Layout Patterns

- **4px base grid** (standard for shadcn/Tailwind systems)
- **Card padding:** Generous internal spacing (~16-24px) despite information density
- **Card gaps:** Consistent gap between card grids (~16px)
- **Sidebar width:** Standard shadcn sidebar (~256px collapsed, ~280px expanded)
- **Content max-width:** Dashboard fills available width (no narrow content column — this is a data tool, not a blog)

---

## 4. UI Patterns Inventory

### Layout Architecture

| Pattern | Description |
|---|---|
| **App Shell** | Sidebar (left) + main content area. Standard shadcn sidebar layout with collapsible behavior. |
| **Dashboard Grid** | Multi-column card grid for metrics and model cards. Responsive columns. |
| **Tabbed Views** | Tabs for category filtering (Model Workbench: model types) and time-range selection (Analytics). |
| **Stacked Sections** | Vertical sections within main content: metrics row → charts → detail cards. |

### Component Inventory

#### Navigation & Chrome

| Component | Implementation | Notes |
|---|---|---|
| **Sidebar** | shadcn Sidebar (full system) | Logo top, nav items with Lucide icons, section labels as group headers, user avatar/section bottom-pinned |
| **Tab Bar** | shadcn Tabs + custom motion-highlight | Animated highlight that follows active tab. Used for workbench categories and analytics time ranges. |
| **Dropdown Menu** | shadcn Dropdown Menu | Context menus, user menu |
| **Tooltip** | shadcn Tooltip | Icon-only actions, truncated text |

#### Data Display

| Component | Implementation | Notes |
|---|---|---|
| **Metric Card** | Custom (Card-based) | Uppercase tracked label, large value, muted delta/subtext. Border-based elevation. |
| **Model Card** | Custom (Card-based) | Model name, config badges (STATIC/FLEX, quantization level), status indicator, action buttons |
| **Area Chart** | Chart component (likely Recharts or similar) | Cyan accent, gradient fill from cyan to transparent. Used for throughput/latency over time. `--chart-1` through `--chart-5` tokens. |
| **Badge** | shadcn Badge | Multiple variants: cyan (primary/active), amber/orange (cloud/shared), muted (default). Uppercase text in some contexts. |
| **Avatar** | shadcn Avatar | User section in sidebar |
| **Separator** | shadcn Separator | Section dividers |

#### Interactive

| Component | Implementation | Notes |
|---|---|---|
| **Button** | shadcn Button | Multiple variants observed: primary (cyan), outline, ghost |
| **Card (interactive)** | shadcn Card | Clickable model cards with hover state |

#### Custom Components

| Component | Description |
|---|---|
| **Motion Highlight** | Animated tab indicator — a highlight element that slides/morphs to follow the active tab. Custom implementation, not from shadcn. Signature interaction pattern. |

### Lucide Icons in Use

33+ Lucide React icons active across the UI. Used consistently for:
- Sidebar navigation items
- Metric card decorators
- Action buttons
- Status indicators
- Model type identifiers

### Visual Hierarchy System

The UI builds hierarchy through a **layered surface model**:

```
Level 0: --background (deepest, page-level)
  Level 1: --card (primary content surfaces, +1-2% luminance)
    Level 2: --muted or --secondary (nested surfaces, hover states)
      Level 3: --accent or --primary (active/focus states, data highlights)
```

**Borders are the primary elevation cue**, not shadows. Every card has a visible `--border` that separates it from its parent surface. This creates a technical, precise aesthetic — flat but structured.

---

## 5. Technical Architecture

### Full Stack Breakdown

| Layer | Technology | Notes |
|---|---|---|
| **ML Runtime** | MLX (Apple ML framework) | Apple's native ML framework for M-series. Leverages unified memory architecture. |
| **API Server** | Python / Uvicorn | ASGI server. Serves three API styles (see below). |
| **Frontend Framework** | Next.js 16.1.1 | App Router, React Server Components, Turbopack bundler |
| **React** | v19 | Latest — server components, use() hook, concurrent features |
| **CSS** | Tailwind CSS v4 | OKLCH color values native. New CSS-first config. |
| **Component Library** | shadcn/ui | data-slot attributes, Radix primitives underneath |
| **Icons** | Lucide React | 33+ icons in active use |
| **Fonts** | GeneralSans + HubotSans Black | Self-hosted WOFF2 via next/font |
| **Deployment** | Vercel | Frontend hosting |
| **Auth** | Custom | Routes through Google App Engine (`launchco.uc.r.appspot.com`) |
| **Database** | CourierDB (custom, local) | Local-first data store |
| **Database** | FlowDB (custom, lightweight) | Secondary lightweight store |
| **MCP Server** | SSE transport | Endpoint: `<base_url>/mcp/sse` |

### API Surface

Courier serves three API styles from the same backend:

| Style | Description | Use Case |
|---|---|---|
| **Courier Custom** | Native API format | Full feature access, Courier-specific features |
| **OpenAI-compatible (Completions)** | Drop-in `/v1/chat/completions` | Swap cloud OpenAI for local with zero code changes |
| **OpenAI-compatible (Responses)** | Drop-in `/v1/responses` | Newer OpenAI Responses API compatibility |

This triple-API approach is strategically important: it eliminates migration friction. Any app using OpenAI's SDK can point at a Courier endpoint instead.

### MCP Integration

- **Transport:** Server-Sent Events (SSE)
- **Endpoint:** `<base_url>/mcp/sse`
- Enables Courier to act as a tool provider for MCP-compatible agents and clients (Claude Desktop, etc.)

### Model Management Concepts

| Concept | Description |
|---|---|
| **STATIC** | Fixed model configuration — loaded and pinned in memory |
| **FLEX** | Dynamic model loading — loaded on demand, can be swapped |
| **Quantization** | Models served at various quantization levels (4-bit, 8-bit, etc.) — visible in UI badges |
| **Model Workbench** | Central UI for browsing, configuring, and managing available models |

---

## 6. Competitive Context

### Market Position

Courier occupies a specific niche: **local-first AI inference with production API compatibility**. It is not competing head-to-head with cloud AI providers on model quality — it's competing on economics, privacy, and control.

### Competitive Landscape

| Competitor | Type | Courier's Differentiator |
|---|---|---|
| **OpenAI API** | Cloud inference | Zero cost per token, data stays local, OpenAI-compatible so migration is trivial |
| **Anthropic API** | Cloud inference | Same as above — local, private, zero marginal cost |
| **Ollama** | Local inference (CLI) | Courier adds production API server, dashboard UI, analytics, model management. Ollama is CLI-first, Courier is platform-first. |
| **LM Studio** | Local inference (GUI) | Similar space but Courier emphasizes API-serving and production readiness over chat UI. Developer-tool positioning vs. consumer-app positioning. |
| **llama.cpp / vLLM** | Raw inference engines | Courier wraps MLX with production chrome: monitoring, management, compatible APIs. Lower barrier to entry. |
| **Together AI / Groq** | Cloud inference (fast) | Speed advantage varies, but Courier wins on cost and privacy for sustained workloads |

### Strategic Moats

1. **Apple Silicon optimization via MLX** — not a generic GGUF runner. Purpose-built for M-series unified memory.
2. **Triple API compatibility** — zero migration cost from OpenAI. This is the critical adoption wedge.
3. **MCP server built-in** — future-proofed for the agentic ecosystem. Courier instances become tool providers automatically.
4. **Dashboard + analytics** — not just serving, but observability. Throughput, latency, model health — production concerns handled.
5. **Bootstrapped / indie** — can move fast, opinionated product decisions, no enterprise bloat pressure.

### Positioning Summary

Courier is to cloud AI APIs what self-hosted databases were to DBaaS: same interface, your hardware, your data, your economics. The product bets that local compute on Apple Silicon is "good enough" for a large class of AI workloads — and for those workloads, the cost and privacy advantages are decisive.

---

## Appendix: Design System Implications

Key constraints and opportunities for downstream design work:

| Constraint | Implication |
|---|---|
| Dark-first, possibly dark-only | Design system should treat dark as canonical. Light mode is optional/derivative. |
| OKLCH color space | Token values must be OKLCH-native. No hex/HSL conversions — use the format the stack expects. |
| Tailwind v4 | CSS-first config, `@theme` directive, no `tailwind.config.js` for color definitions. |
| shadcn/ui with data-slot | Components use `data-slot` attributes for styling hooks. Design tokens must align with shadcn's variable naming convention. |
| Information-dense dashboard | Components need to perform at small sizes. Typography scale must support dense metric displays without sacrificing legibility. |
| Border-based hierarchy (no shadows) | Elevation system is luminance + border, not shadow-based. This is a deliberate aesthetic choice to maintain. |
| Two-font system | GeneralSans (utility) + HubotSans Black (impact). Clear separation of roles — don't blur them. |
| Cyan/teal + amber/orange | Two-accent system. Primary (cyan) for actions/data, secondary (amber) for status/categorization. Do not introduce a third accent without strong justification. |
