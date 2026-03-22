# Courier — Claude Code Instructions

Courier is a local AI model serving platform for Apple Silicon. Turns M-series Macs into production-ready AI API platforms.

## Quality Standards

Read `constitution/ENGINEERING-CONSTITUTION.md` before making architectural decisions. Quick version: `constitution/QUICK-REFERENCE.md`.

**Quality bar:** Linear + Kalshi. Data-dense, dark-first, every pixel intentional. No marketing language in product chrome.

## Stack

- **Framework:** Next.js 16.1.1 (App Router, RSC, Turbopack), React 19
- **Styling:** Tailwind CSS v4 (OKLCH color space), shadcn/ui (data-slot, Radix)
- **Icons:** Lucide React
- **Fonts:** GeneralSans (body) + HubotSans Black 900 (headings) + system mono (code)
- **Color identity:** Cyan/teal primary, amber/orange secondary
- **Theme:** Dark-first (`class="dark"` on html). Light theme exists but dark is the design context.

## Design System

Run `design-system/prompts/00-09` in order to build the full token and component system. The `skills/ui-engineering/` package triggers automatically on any UI work.

**References inside the skill:**
- `references/design-tokens.md` — Token architecture
- `references/component-patterns.md` — Component API patterns
- `references/quality-checklist.md` — Pre-commit UI audit

## Product Context

Read `product-brief/COURIER-PRODUCT-BRIEF.md` before planning features. Based on real usage data — not hypotheticals.

## Anti-Patterns (Abbreviated)

| Never | Instead |
|-------|---------|
| Shadows for hierarchy on dark backgrounds | Surface luminance stepping + borders |
| `rounded-full` / `rounded-xl` everywhere | `rounded-md` cards, `rounded-sm` inputs, sharp tables |
| Marketing copy in product chrome | Functional labels only |
| Softening technical language | Users know what Q4_K_M means |
| Animating high-frequency updates | Reserve motion for user-initiated transitions |
| Hiding config behind "simple mode" | Surface config directly with smart defaults |
| Rainbow chart palettes | Perceptually uniform, max 6 categorical hues |

## Interaction Rules

- **Hover:** `150ms ease-out`, subtle background shift only. Guard with `@media (hover: hover)`.
- **Active/press:** `scale(0.97)` + `50ms` for tactile click. Buttons and interactive cards only.
- **Focus rings:** `ring-2 ring-ring ring-offset-2 ring-offset-background` on all interactive elements. Never remove.
- **Numeric data:** Always `tabular-nums` for alignment in tables and metrics.

## Typography Hierarchy

- `text-xs` — metadata, timestamps, secondary labels
- `text-sm` — body text, descriptions, form labels
- `text-base` — emphasis, primary content
- `text-lg`+ — section headers only (use sparingly)
- Headings: HubotSans Black 900. Body: GeneralSans. Code: system mono. No exceptions.

## Border-Based Depth Model

```
slate-950 (background) → slate-900 (surface) → slate-800 (elevated)
Borders: 1px slate-700/50 for separation
Glow: Reserved for active/focus states only
```
