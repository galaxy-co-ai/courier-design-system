# Design System Audit: Athlete Era + Results Roofing → Courier

**Date:** 2026-03-21
**Primary Reference:** Athlete Era (`athlete-era/src/styles/globals.css`)
**Secondary Reference:** Results Roofing (multi-file token architecture)
**Target:** Courier design system construction

---

## 1. Athlete Era Architecture Breakdown

### What Makes This Design System Exceptional

Athlete Era's `globals.css` is a single-file, 815-line design system that achieves something rare: it is simultaneously exhaustive and readable. Every decision is deliberate, every token is used, and the system self-documents through naming alone. Here is why it works.

---

### Token Organization Strategy

The file follows a strict declaration order that mirrors how a designer thinks about a UI:

1. **Text system** (most frequently referenced, declared first)
2. **Gray scale** (structural backbone)
3. **Brand + signal colors** (identity + semantics)
4. **Surfaces** (backgrounds, cards, overlays)
5. **Borders** (structural separation)
6. **Shadows** (elevation language)
7. **Spacing** (layout rhythm)
8. **Typography** (weight + tracking discipline)
9. **Motion** (interaction timing)
10. **Component classes** (assembled from all the above)

This order means any component class at the bottom of the file references tokens declared above it. You never scroll up wondering "where did this come from?" The dependency graph flows downward.

The naming convention is flat and direct: `--t1`, `--gray-6`, `--brand`, `--bg-elevated`, `--border-mid`, `--shadow-md`, `--space-4`. No nested BEM, no utility prefixes, no abstraction layers. A developer reads `var(--t2)` and knows it means "secondary text" without consulting documentation.

---

### The Text Opacity System

This is the single most important architectural decision in the system, and it is the one most design systems get wrong.

**How most systems handle text hierarchy:**
```css
--text-primary: #e5e5e5;
--text-secondary: #a3a3a3;
--text-muted: #737373;
```

Three discrete gray values that look fine on one background but break on another. Put `--text-secondary` on a card with a slightly different background tint, and the contrast ratio shifts unpredictably.

**How Athlete Era handles it:**
```css
/* Light */
--t1: rgba(0, 0, 0, 0.9);
--t2: rgba(0, 0, 0, 0.5);
--t3: rgba(0, 0, 0, 0.3);

/* Dark */
--t1: rgba(255, 255, 255, 0.93);
--t2: rgba(255, 255, 255, 0.55);
--t3: rgba(255, 255, 255, 0.35);
```

Alpha-based text renders through the background color. On `--bg` it looks one way; on `--bg-elevated` it looks slightly different, but the *perceived contrast ratio remains stable* because the opacity relationship is constant. This means:

- Text hierarchy is consistent across every surface automatically
- You never need surface-specific text color overrides
- Dark mode "just works" because you flip black→white base, same alpha ratios
- The gray scale aliases `--gray-11` and `--gray-12` to `--t2` and `--t1`, so components using the gray scale and components using text tokens are visually harmonized

This is the technique Courier must adopt. It is especially critical for Courier's dark-first theme where multiple card surfaces at slightly different elevations all need consistent text readability.

---

### Dark/Light Mode Switching Approach

Athlete Era uses a `[data-theme="dark"]` selector with full token redeclaration. The key insight: **dark mode is not an inversion, it is a separate palette with shared architecture.**

Light mode uses shadows for elevation. Dark mode uses surface lightness for elevation. This is encoded in the tokens themselves:

- Light: `--bg: #FFFFFF`, `--bg-elevated: hsl(...)` (slightly tinted white)
- Dark: `--bg: hsl(220, 15%, 8%)`, `--bg-elevated: hsl(220, 10%, 12%)` (lighter = higher)

Shadow tokens also shift: light mode has a full shadow scale, dark mode flattens most shadows to near-zero and relies on border + surface contrast instead. This is not a shortcut; it is how perception works. Shadows on dark backgrounds are invisible. Surface differentiation replaces them.

The `--bg-sunken` token (darker than `--bg`) creates visual depth for page-level backgrounds behind cards. The `--bg-overlay` token handles modals and sheets. Four surfaces, clearly ordered by elevation, both modes.

The `data-theme="dark-immersive"` variant goes further, pushing surfaces even darker for media-focused views. This is a luxury most systems skip, but it proves the architecture supports theming beyond two modes.

---

### Component-Level CSS Classes

Athlete Era does something unusual: it ships component CSS classes directly in `globals.css` instead of relying on Tailwind utilities alone. These classes are not component *libraries*; they are interaction behavior contracts.

**Example: `.market-card`**
```css
.market-card {
  border: 1px solid var(--border);
  border-radius: var(--card-r);
  background: var(--bg-elevated);
  box-shadow: var(--shadow-sm);
  transition: box-shadow var(--duration-normal) var(--ease-out),
              border-color var(--duration-normal) var(--ease-out);
}
```

With hover guard:
```css
@media (hover: hover) {
  .market-card:hover {
    box-shadow: var(--shadow-hover);
    border-color: var(--border-mid);
  }
}
```

This encodes:
1. **Visual state** (bg, border, shadow, radius)
2. **Transition properties** (which properties animate, at what speed)
3. **Hover behavior** (elevation shift + border emphasis)
4. **Touch safety** (hover guard prevents sticky-hover on mobile)

All in one place. The developer writes `className="market-card"` and gets correct behavior on every device. No remembering to add `hover:shadow-md transition-shadow duration-200` in Tailwind.

Other notable patterns:
- `.odds-pill` uses `data-variant="yes|no"` for signal color switching — no conditional className logic
- `.category-tab` has `transition: none` because high-frequency interactions (tab switching) should feel instant
- `.card-hover-actions` uses `opacity: 0` → `opacity: 1` on parent hover, keeping actions in DOM for accessibility
- `.btn-primary:active { transform: scale(0.97) }` — subtle press feedback, not bounce

---

### The Tailwind v4 @theme inline Bridge

This is the mechanism that makes the token system usable in both CSS and Tailwind utility contexts:

```css
@theme inline {
  --color-gray-1: var(--gray-1);
  --color-gray-2: var(--gray-2);
  /* ... through gray-12 */
  --color-t1: var(--t1);
  --color-t2: var(--t2);
  --color-t3: var(--t3);
  --color-brand: var(--brand);
  --shadow-sm: var(--shadow-sm);
  --radius-card: var(--card-r);
  /* etc. */
}
```

This maps every CSS custom property to a Tailwind v4 theme token. The result: `text-t1`, `bg-gray-3`, `shadow-sm`, `rounded-card` all work as utility classes while pulling from the same CSS variables that component classes use. One source of truth, two consumption paths.

The `inline` keyword is significant — it tells Tailwind v4 to generate these utilities without writing to a separate theme file. The tokens exist in CSS, Tailwind reads them, no build step duplication.

---

### Motion and Interaction Philosophy

Athlete Era's motion system is opinionated:

| Token | Value | Use Case |
|-------|-------|----------|
| `--duration-instant` | 100ms | Opacity toggles, icon swaps |
| `--duration-fast` | 150ms | Hover states, button feedback |
| `--duration-normal` | 200ms | Card transitions, focus rings |
| `--duration-smooth` | 300ms | Modals, sheets, page transitions |
| `--duration-exit` | 210ms | Slightly faster than enter (perceived speed) |

Three easing curves:
- `--ease-out`: deceleration (entering elements)
- `--ease-in`: acceleration (exiting elements)
- `--ease-in-out`: symmetric (looping, reversible)

All cubic-bezier, not CSS keyword easings. This matters for perceived quality.

The reduced motion system is comprehensive: `@media (prefers-reduced-motion: reduce)` kills transitions and animations globally, and the `.theme-switching` class does the same during theme changes to prevent flash-of-transition.

Active press uses `scale(0.97)` — small enough to feel tactile without looking "bouncy." Disabled states use `opacity: 0.5` and `cursor: not-allowed` consistently. Focus uses `box-shadow` double-ring (background color inner + brand outer), never `outline`, for consistent rounded-corner appearance.

---

## 2. Results Roofing Architecture Breakdown

### What to Borrow

Results Roofing takes a different structural approach: 9 dedicated files imported through a single `index.css`. While less elegant than Athlete Era's monolith, this architecture has two significant advantages for larger teams and longer-lived projects.

---

### Multi-File Separation

```
tokens/
├── index.css          # Single import point
├── colors.css         # Brand, neutrals, status, semantic
├── typography.css     # Families, sizes, weights, line-heights
├── spacing.css        # 4px grid, semantic spacing
├── radii.css          # Border radius scale
├── shadows.css        # Elevation scale
├── animations.css     # Duration, easing, composed transitions
├── z-index.css        # Layering scale
└── components.css     # Sizing constraints (not visual styles)
```

Benefits:
- **Git diffs are scoped.** Changing a shadow value only touches `shadows.css`. In a monolith, the diff appears in a 800-line file and reviewers must hunt for the actual change.
- **Mental model is file-level.** "Where are the spacing tokens?" → `spacing.css`. No scrolling, no searching.
- **Parallel editing.** Two developers can work on `colors.css` and `typography.css` without merge conflicts.

Drawbacks:
- Import order matters (CSS cascade). A misordered import can cause subtle precedence bugs.
- Cross-references between files are invisible. `components.css` references tokens from `spacing.css` and `colors.css`, but that dependency is implicit.
- Nine files for tokens is overkill for a project with one developer. The overhead only pays off with 3+ contributors.

---

### The 3-Layer Naming Convention

This is Results Roofing's strongest architectural contribution:

```
Layer 1 — Primitive:    --blue-600: #2563EB
Layer 2 — Semantic:     --color-primary: var(--blue-600)
Layer 3 — Component:    --btn-primary-bg: var(--color-primary)
```

Each layer serves a different audience:
- **Primitives** are for the design system maintainer. They define the raw palette.
- **Semantic tokens** are for developers building features. They express intent: "this is the primary action color."
- **Component tokens** are for component authors. They allow per-component overrides without breaking the semantic layer.

In practice, most projects only need two layers. The third layer (component tokens) is valuable when:
- The same semantic token maps to different visual treatments in different components (e.g., `--color-primary` might be the button fill but a card's border accent)
- You need to support white-labeling or per-client theming

For Courier, the 3-layer convention is worth adopting for colors (where the semantic→component mapping is complex) but overkill for spacing and typography (where the semantic layer is sufficient).

---

## 3. Courier Adaptation Plan

### Guiding Principle

Courier already has a personality: dark, cold, technical, infrastructure-oriented. The adaptation must feel like Courier grew up, got more precise, more considered — not like it got reskinned with another product's aesthetic.

Athlete Era is a prediction market with a Kalshi-inspired identity. Results Roofing is a service business with a professional-blue identity. Courier is an AI infrastructure dashboard. Different products, same engineering rigor underneath.

---

### COLOR DIRECTION

#### Keep — These Are Courier's Identity

| Token Category | Current Direction | Notes |
|----------------|-------------------|-------|
| Backgrounds | Deep slate/charcoal, near-black, cold-toned | This IS the infrastructure dashboard feel |
| Primary accent | Cyan/teal | Technical, precise, data-forward |
| Secondary accent | Amber/orange | Alert/badge energy, warm contrast to cold bg |
| Color format | OKLCH values | Already in use, superior to HSL for perceptual uniformity |

#### Elevate — Build Professional Infrastructure

**12-Step Gray Scale with Cool Slate Undertone**

Athlete Era uses neutral grays (no hue bias). Courier should build its scale with a cool slate undertone (blue-ish, ~220-230 hue in HSL, or equivalent in OKLCH) to reinforce the cold-tech personality.

Proposed OKLCH gray scale (dark mode primary):

```css
--gray-1:  oklch(0.14 0.005 250);   /* Deepest bg, near-black with blue cast */
--gray-2:  oklch(0.17 0.005 250);   /* Sunken surfaces */
--gray-3:  oklch(0.21 0.005 250);   /* Default bg */
--gray-4:  oklch(0.25 0.004 250);   /* Elevated surfaces */
--gray-5:  oklch(0.29 0.004 250);   /* Higher elevation */
--gray-6:  oklch(0.33 0.004 250);   /* Subtle borders, dividers */
--gray-7:  oklch(0.40 0.003 250);   /* Default borders */
--gray-8:  oklch(0.48 0.003 250);   /* Strong borders, scrollbar thumb */
--gray-9:  oklch(0.56 0.002 250);   /* Placeholder text */
--gray-10: oklch(0.64 0.002 250);   /* Tertiary text */
--gray-11: oklch(0.74 0.001 250);   /* Secondary text */
--gray-12: oklch(0.90 0.000 0);     /* Primary text (near-white, no hue) */
```

Key difference from Athlete Era: the chroma (saturation) is subtle but present in steps 1-8, creating a cohesive cold tone. Steps 11-12 desaturate toward pure white for text readability. Athlete Era's grays are neutral; Courier's should feel like they belong in a server room.

**Alpha-Based Text Tokens (Tuned for Dark Backgrounds)**

```css
--t1: oklch(1.00 0 0 / 0.92);    /* Primary text — slightly softer than pure white */
--t2: oklch(1.00 0 0 / 0.56);    /* Secondary text — labels, descriptions */
--t3: oklch(1.00 0 0 / 0.34);    /* Tertiary text — placeholders, timestamps */
```

These should alias to `--gray-12`, `--gray-11`, and `--gray-10` respectively for consistency, exactly as Athlete Era does.

For the light mode (if/when added):
```css
--t1: oklch(0.00 0 0 / 0.88);
--t2: oklch(0.00 0 0 / 0.52);
--t3: oklch(0.00 0 0 / 0.30);
```

**Signal / Status Colors with 10% Alpha Variants**

Courier needs infrastructure-appropriate signals, not market-appropriate ones:

| Signal | Hex | Use Case |
|--------|-----|----------|
| `--success` | `#10B981` (emerald) | Healthy, deployed, connected |
| `--success-10` | 10% alpha variant | Success badge backgrounds |
| `--warning` | `#F59E0B` (amber) | Degraded, slow, approaching limit |
| `--warning-10` | 10% alpha variant | Warning badge backgrounds |
| `--error` | `#EF4444` (red) | Failed, error, disconnected |
| `--error-10` | 10% alpha variant | Error badge backgrounds |
| `--info` | `var(--brand)` (cyan) | Informational, in-progress |
| `--info-10` | 10% alpha variant | Info badge backgrounds |

Note: Athlete Era uses `--yes/--no/--up/--down` because it is a prediction market. Courier should use `--success/--warning/--error/--info` because it is an infrastructure dashboard. Same pattern, domain-appropriate naming.

**Chart Colors That Complement Cyan**

Courier's charts need a palette that does not compete with the cyan primary. Recommendation: a sequential palette anchored to the brand, with categorical divergence through hue rotation.

```css
--chart-1: var(--brand);                   /* Cyan — primary metric */
--chart-2: oklch(0.72 0.14 180);           /* Teal — secondary metric */
--chart-3: oklch(0.72 0.14 290);           /* Violet — third metric */
--chart-4: oklch(0.75 0.12 60);            /* Amber — fourth metric */
--chart-5: oklch(0.70 0.14 330);           /* Rose — fifth metric */
--chart-6: oklch(0.72 0.10 140);           /* Sage — sixth metric */
```

OKLCH hue rotation ensures perceptual uniformity (equal visual "loudness") across all chart colors. The chroma is kept moderate so charts feel integrated with the UI, not like a rainbow landed on a dark dashboard.

---

### ARCHITECTURE DECISION

**Recommendation: Single-file with section comments, Athlete Era pattern.**

Rationale:
1. Courier is a single-developer project (for now). Multi-file overhead is not justified.
2. The dependency flow is clearer in a single file — you can see how text tokens, gray scale, and surface tokens relate without switching files.
3. Athlete Era proves that 800+ lines in a single file is manageable with disciplined section organization.
4. If Courier grows to 3+ contributors, the file can be split later. Going multi-file→single-file is harder than the reverse.

**File structure:**
```
src/styles/
├── globals.css           # Design tokens + component classes + @theme bridge
└── fonts.css             # @font-face declarations (GeneralSans, HubotSans)
```

**Section order within `globals.css`:**
```css
/* ═══════════════════════════════════════════
   1. TEXT SYSTEM (alpha-based)
   ═══════════════════════════════════════════ */

/* ═══════════════════════════════════════════
   2. GRAY SCALE (12-step, cool slate)
   ═══════════════════════════════════════════ */

/* ═══════════════════════════════════════════
   3. BRAND + SIGNAL COLORS
   ═══════════════════════════════════════════ */

/* ═══════════════════════════════════════════
   4. SURFACES (bg, elevated, sunken, overlay)
   ═══════════════════════════════════════════ */

/* ═══════════════════════════════════════════
   5. BORDERS (alpha-based)
   ═══════════════════════════════════════════ */

/* ═══════════════════════════════════════════
   6. SHADOWS + ELEVATION
   ═══════════════════════════════════════════ */

/* ═══════════════════════════════════════════
   7. SPACING + LAYOUT
   ═══════════════════════════════════════════ */

/* ═══════════════════════════════════════════
   8. TYPOGRAPHY (weights, tracking, scale)
   ═══════════════════════════════════════════ */

/* ═══════════════════════════════════════════
   9. MOTION + INTERACTION
   ═══════════════════════════════════════════ */

/* ═══════════════════════════════════════════
   10. COMPONENT CLASSES
   ═══════════════════════════════════════════ */

/* ═══════════════════════════════════════════
   11. TAILWIND v4 @theme inline BRIDGE
   ═══════════════════════════════════════════ */

/* ═══════════════════════════════════════════
   12. UTILITY CLASSES + OVERRIDES
   ═══════════════════════════════════════════ */
```

**shadcn CSS Variable Integration:**

Courier already uses shadcn's CSS variable convention (`--background`, `--foreground`, `--card`, `--primary`, etc.). The richer token system should wrap, not replace, shadcn's variables:

```css
/* shadcn compatibility layer */
--background: var(--bg);
--foreground: var(--t1);
--card: var(--bg-elevated);
--card-foreground: var(--t1);
--primary: var(--brand);
--primary-foreground: var(--gray-1);
--secondary: var(--gray-4);
--secondary-foreground: var(--t1);
--muted: var(--gray-3);
--muted-foreground: var(--t2);
--accent: var(--gray-4);
--accent-foreground: var(--t1);
--destructive: var(--error);
--destructive-foreground: #ffffff;
--border: var(--border);
--input: var(--border-mid);
--ring: var(--brand);
```

This means shadcn components work out of the box with `bg-background`, `text-foreground`, etc., but Courier's own components can use the richer token vocabulary (`bg-gray-4`, `text-t2`, `border-border-mid`). Two vocabularies, one source, zero conflicts.

---

## 4. Gap Analysis

### What Courier Needs That Athlete Era Does Not Cover

Athlete Era is a consumer-facing prediction market. Courier is an infrastructure dashboard. The interaction patterns, information density, and visual language differ in specific, addressable ways.

---

### Infrastructure / DevOps UI Patterns

**Model Cards**
Athlete Era's `.market-card` is a good starting point but lacks:
- Status indicator integration (online/offline/degraded dot or badge)
- Metric display areas (latency, token count, cost per call)
- Configuration summary (model name, provider, version)
- Action overflow menus (configure, disable, set as default)

Courier needs a `.model-card` component class that bakes in:
```css
.model-card {
  /* Base: card token system from Athlete Era */
  /* Added: status indicator position (top-right absolute) */
  /* Added: metric row layout (flex, space-between, tabular-nums) */
  /* Added: provider badge slot (top-left) */
  /* Added: data-status="online|offline|degraded" for border-left color coding */
}
```

**Deployment Status Indicators**
Not present in Athlete Era. Courier needs:
- Pulse animation for "deploying" state (similar to Athlete Era's `.live-badge` pulse but in status color)
- Static dot for stable states with signal-color coding
- Timeline/log display for deployment history

**API Configuration Panels**
Athlete Era has no equivalent. Courier needs:
- Key/value display with monospace font for values
- Copy-to-clipboard interaction pattern
- Secret masking (show/hide toggle)
- Environment selector (production/staging/development)

**Memory Management Displays**
Unique to Courier. Needs:
- Progress bars / usage meters with threshold warnings
- Capacity planning visualizations (projected vs actual)
- Token for "capacity" states: `--capacity-safe`, `--capacity-warn`, `--capacity-critical`

---

### Analytics Dashboard Patterns

**Metric Cards (KPI Tiles)**
Athlete Era's `.delta-up` / `.delta-down` pattern is directly transferable but needs expansion:
- Large number display (32-40px, tabular-nums, weight-600)
- Delta badge with directional arrow
- Sparkline integration area
- Time period selector (24h / 7d / 30d)

Token needs:
```css
--metric-value-size: var(--type-3xl);
--metric-label-size: var(--type-xs);
--metric-delta-size: var(--type-sm);
```

**Time-Series Charts**
Athlete Era has chart color tokens but no chart-specific layout tokens. Courier needs:
- Chart container with consistent padding and border treatment
- Axis label styling (--t3, monospace, small)
- Grid line color (border at very low alpha, ~0.04)
- Tooltip styling tokens (bg-overlay, shadow-md, small type)
- Legend item layout tokens

```css
--chart-grid: oklch(1 0 0 / 0.04);
--chart-axis-color: var(--t3);
--chart-axis-font: var(--font-mono);
--chart-axis-size: 10px;
--chart-tooltip-bg: var(--bg-overlay);
--chart-tooltip-shadow: var(--shadow-md);
--chart-tooltip-radius: var(--radius-md);
```

**Data Tables**
Athlete Era uses cards, not tables. Courier's infrastructure data (model configs, API keys, deployment logs) needs:
- Row hover with subtle bg shift
- Sortable column header indicators
- Cell alignment tokens (numbers right-aligned, tabular-nums)
- Compact vs comfortable density toggle (row heights: 36px / 48px)
- Sticky header treatment

---

### MCP / API Documentation Display Patterns

**Endpoint Cards**
Need to display tool definitions, parameter schemas, response shapes:
- Method badge (GET/POST/PUT/DELETE with semantic color coding)
- Path display in monospace
- Parameter list with type annotations
- Collapsible request/response body sections

**Connection Status Widgets**
MCP server connectivity display:
- Server name + status dot
- Last ping / latency display
- Tool count badge
- Reconnect action

**Code Blocks**
Athlete Era has no code display system. Courier needs:
- Syntax-highlighted code blocks with dark bg (darker than card bg)
- Copy button positioning
- Language badge
- Line numbers (optional, monospace, --t3)
- Diff highlighting (added/removed line bg colors)

Token needs:
```css
--code-bg: var(--gray-2);           /* Slightly darker than card bg */
--code-border: var(--border);
--code-font: var(--font-mono);
--code-size: 13px;
--code-line-height: 1.6;
--code-selection: oklch(0.5 0.1 200 / 0.3);  /* Cyan-tinted selection */
```

---

### Summary: Token Categories Courier Must Add Beyond Athlete Era

| Category | Tokens Needed | Athlete Era Has? |
|----------|---------------|------------------|
| Status colors (4 states) | `--success`, `--warning`, `--error`, `--info` + alpha variants | Partial (has signal colors, but market-named) |
| Capacity thresholds | `--capacity-safe/warn/critical` | No |
| Chart infrastructure | Grid, axis, tooltip tokens | Partial (colors only) |
| Code block system | bg, border, font, sizing | No |
| Data table density | Compact/comfortable row heights | No |
| Monospace font token | `--font-mono` | No (uses OpenType features but one font family) |
| API method colors | GET/POST/PUT/DELETE badge colors | No |
| Badge system | Size variants, status-colored backgrounds | Partial (`.live-badge` only) |
| Progress/meter tokens | Bar colors, thresholds, track bg | No |
| Connection state tokens | Connected/disconnected/reconnecting | No |

---

### Final Architecture Summary

```
ATHLETE ERA CONTRIBUTION           COURIER ADAPTATION
─────────────────────────          ─────────────────────
Alpha text system (--t1/t2/t3)  →  Same pattern, OKLCH values, tuned for dark-first
12-step gray scale              →  Cool slate undertone (hue ~250 OKLCH), not neutral
Signal colors + alpha variants  →  Infrastructure naming (success/warning/error/info)
Surface elevation (4 levels)    →  Same pattern, colder tones
Alpha borders (3 levels)        →  Same pattern
Shadow scale                    →  Minimal (dark-first = surface elevation, not shadow)
4px spacing grid                →  Same, add infrastructure-specific semantic spacing
Type scale classes              →  Same, add --font-mono and code-specific scale
Motion system                   →  Same tokens, same philosophy
Component CSS classes           →  Adapted for infrastructure UI patterns
@theme inline bridge            →  Same, with shadcn compatibility layer
Hover guards + touch safety     →  Same

RESULTS ROOFING CONTRIBUTION       COURIER ADAPTATION
────────────────────────────       ─────────────────────
3-layer naming (primitive →        Adopt for colors (complex mapping)
  semantic → component)            Skip for spacing/typography (overkill)
Multi-file separation           →  Skip (single dev, single file for now)
Component sizing constraints    →  Adopt (button/input/modal size tokens)
z-index scale                   →  Adopt (missing from Athlete Era)

COURIER-ORIGINAL (no precedent)
────────────────────────────────
Infrastructure status system
Chart infrastructure tokens
Code block system
Data table density tokens
API method color coding
Capacity/threshold visualization
MCP connection state display
```

This audit provides the specification for building Courier's `globals.css`. Every token decision above should be implemented, validated against actual components, and adjusted during build — but the architecture is locked.
