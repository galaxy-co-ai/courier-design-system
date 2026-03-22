---
name: courier-ui-engineering
description: Triggers on any frontend, UI, component, layout, or styling work for Courier. Teaches Claude Code Courier's design language, component patterns, and quality bar.
triggers:
  - frontend
  - UI
  - component
  - layout
  - style
  - CSS
  - Tailwind
  - shadcn
  - design system
  - dark mode
  - chart
  - dashboard
  - metric
  - model card
---

# Courier UI Engineering

Courier is a local AI model serving platform for Apple Silicon. Turns M-series Macs into production-ready AI API platforms. Built by Jackson at RecursionAI, 100% bootstrapped.

**Stack:** Next.js 16.1.1 (App Router, RSC, Turbopack), React 19, Tailwind CSS v4 (OKLCH), shadcn/ui (data-slot, Radix), Lucide React (33+ icons), Vercel deploy, dark-first (`class="dark"` on html).

**Fonts:** GeneralSans (body, WOFF2) + HubotSans Black 900 (headings, WOFF2) + system mono (code). Loaded via next/font.

---

## 1. Design Language

- **Dark-first, data-dense infrastructure dashboard.** Light theme exists but dark is the primary testing and design context.
- **Cyan/teal primary accent** — actions, links, active states, focus rings.
- **Amber/orange secondary accent** — warnings, deltas, attention signals.
- **Border-based hierarchy** — not shadows. On dark backgrounds, borders define structure; shadows are invisible. Use `border-border` for separation, `border-ring` for emphasis.
- **Information-dense but not cluttered.** Every element earns its space.
- **No marketing language in product chrome.** Labels are functional, not promotional.
- **Quality benchmark: Linear + Kalshi** — fast, data-dense, every pixel intentional.

---

## 2. Component Inventory

### shadcn components in use
Sidebar (full system), Card, Button, Badge, Avatar, Tabs, Tooltip, Dropdown Menu, Separator.

### Custom components
- **motion-highlight** — animated tab highlight that follows active tab position.

### CSS variable convention (shadcn)
Core: `--background`, `--foreground`, `--primary`, `--secondary`, `--muted`, `--accent`, `--destructive`, `--border`, `--ring`, `--radius`.
Surface: `--card`, `--popover`, `--sidebar`, `--sidebar-*` (foreground, primary, accent, border, ring).
Charts: `--chart-1` through `--chart-5`.

All colors use OKLCH values per Tailwind v4.

---

## 3. Font Stack

| Role | Font | Weight | Usage |
|------|------|--------|-------|
| Body | GeneralSans | 400–600 | UI labels, navigation, paragraph text |
| Display | HubotSans Black | 900 | Headings, metric values, display text |
| Code | System mono | 400 | Code blocks, API examples, model IDs |

**Rule:** Three roles, three fonts. Never mix. GeneralSans for body, HubotSans for display, mono for code.

---

## 4. Quality Bar

- **Benchmark:** Linear + Kalshi — if it wouldn't look right in those products, it doesn't ship.
- **All 4 interaction states** on every interactive element: default, hover, active/pressed, disabled.
- **Dark theme is primary** — test there first, verify light second.
- **Monospace + tabular-nums** for all numeric data (metrics, counts, latency, throughput).
- **No orphaned UI** — every component handles empty state, loading state, error state.
- **Responsive** — dashboard must work at 1024px minimum width.

---

## 5. Component Anatomy

Standard pattern for all Courier components:

```tsx
interface MetricCardProps {
  label: string;
  value: string | number;
  delta?: { value: number; direction: 'up' | 'down' };
  className?: string;
}

export function MetricCard({ label, value, delta, className }: MetricCardProps) {
  return (
    <div className={cn(
      'rounded-lg border border-border bg-card p-4',
      className
    )}>
      <p className="text-sm text-muted-foreground font-general-sans">{label}</p>
      <p className="text-2xl font-hubot-sans font-black tabular-nums">{value}</p>
      {delta && (
        <span className={cn(
          'text-xs tabular-nums',
          delta.direction === 'up' ? 'text-chart-2' : 'text-destructive'
        )}>
          {delta.direction === 'up' ? '+' : ''}{delta.value}%
        </span>
      )}
    </div>
  );
}
```

**Key patterns:** typed props, `className` passthrough, `cn()` utility, semantic color tokens only, font classes match role, `tabular-nums` on numbers.

---

## 6. Interaction Philosophy

### Text hierarchy (alpha-based)
Use opacity layers that render through the background:
- `--t1` (primary, full opacity) — headings, active labels, values
- `--t2` (secondary, ~70%) — descriptions, inactive labels
- `--t3` (tertiary, ~50%) — timestamps, metadata, disabled text

### Hover guards
```css
@media (hover: hover) {
  .interactive:hover { background: var(--accent); }
}
```
All hover states wrapped for touch safety. No hover-dependent information.

### Active press
`transform: scale(0.97)` on `button:active` — subtle, physical, not bouncy.

### Focus rings
Double-ring box-shadow (`bg-color + brand-color`), never `outline`. Visible on both themes.

### Tabular numerics
`font-variant-numeric: tabular-nums` on ALL numeric displays — metrics, tables, charts, timestamps, counts.

### Reduced motion
```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

### Theme transition blocker
Apply `.theme-switching` class during theme swap to kill all transitions — prevents flash of animated elements.

### High-frequency actions
No animation on tab switching, list updates, status refreshes, or data polling. Reserve motion for user-initiated actions and state changes.

---

## 7. References

Before building, read the reference files in this skill's directory:

| File | Purpose |
|------|---------|
| `references/design-tokens.md` | Token architecture, naming conventions, scales |
| `references/component-patterns.md` | Component anatomy for every component type |
| `references/quality-checklist.md` | Pre-flight checklist for every component |

---

## 8. Read Before Working

Before ANY UI work on Courier, read these files in order:

1. **This file** (`skills/ui-engineering/SKILL.md`) — design language, patterns, quality bar
2. **Reference files** (`references/design-tokens.md`, `references/component-patterns.md`, `references/quality-checklist.md`)
3. **Project globals.css** (or equivalent token file) — actual token definitions
4. **ENGINEERING-CONSTITUTION.md** — quality standards and engineering principles

Do not skip steps. Do not assume token values — read them from the source.
