# Courier Component Quality Checklist

Pre-flight for every component. Dark-first, infrastructure-grade.

---

## 1. Visual Quality

- [ ] Dark theme is the primary test (test dark FIRST, not light)
- [ ] Text contrast passes WCAG AA against slate-900/950 backgrounds
- [ ] Alpha-based text tokens used (`--t1`, `--t2`, `--t3`), not raw gray values
- [ ] Semantic color tokens used, never raw hex/hsl/oklch values in components
- [ ] Border-based hierarchy (no shadow-only elevation on dark backgrounds)
- [ ] Spacing on 4px grid (no arbitrary pixel values)
- [ ] Typography from type scale (`type-xs` through `type-4xl`), no ad-hoc font sizes
- [ ] HubotSans Black for headings only, GeneralSans for everything else
- [ ] Cyan accent for primary/interactive, amber for status/badges — never crossed
- [ ] No layout shift on data load (skeleton placeholders or fixed-height containers)
- [ ] Icons from Lucide React, consistent size within context (16px nav, 20px actions, 24px display)
- [ ] Monospace font for code values, model IDs, API endpoints, hashes

## 2. Interaction Quality

- [ ] All 4 states defined: default, hover, active/pressed, disabled
- [ ] Hover guard: `@media (hover: hover)` wraps ALL hover states
- [ ] Active press: `transform scale(0.97)` on buttons/clickable cards
- [ ] Disabled: `opacity 0.5`, `cursor not-allowed`, no transform
- [ ] Focus: box-shadow double ring (bg color + brand), never outline
- [ ] Transition tokens used (`--duration-fast`, `--ease-out`), not arbitrary values
- [ ] High-frequency interactions have NO animation (tab switch, list refresh, status update)
- [ ] Touch target minimum 44px for mobile
- [ ] Input minimum 16px font-size (prevents iOS zoom)
- [ ] `-webkit-tap-highlight-color: transparent` on interactive elements
- [ ] `touch-action: manipulation` to prevent double-tap zoom

## 3. Accessibility

- [ ] All interactive elements keyboard-reachable (tab order logical)
- [ ] Focus indicators visible on dark backgrounds (cyan ring, not default browser blue)
- [ ] Screen reader labels on icon-only buttons (`aria-label` or `sr-only` text)
- [ ] Status changes announced via `aria-live` regions
- [ ] Color never the sole indicator (pair with icon, text, or pattern)
- [ ] Reduced motion respected: `@media (prefers-reduced-motion: reduce)` kills transitions
- [ ] Meaningful alt text on informational images (or `aria-hidden` on decorative)
- [ ] Form inputs have associated labels
- [ ] Error messages reference the specific field

## 4. Code Quality

- [ ] TypeScript strict — no `any`, no untyped props
- [ ] Props interface defined and exported (for composition)
- [ ] `className` prop accepted and passed through via `cn()` utility
- [ ] Component uses named export (not default)
- [ ] Imports ordered: react → next → third-party → internal → types
- [ ] No inline styles (use Tailwind utilities or CSS variables)
- [ ] No magic numbers — extract to tokens or named constants
- [ ] Loading state handled (skeleton or spinner)
- [ ] Error state handled (not just happy path)
- [ ] Empty state handled (meaningful message, not blank space)

## 5. Performance

- [ ] Server Component by default (client only if state/effects/events needed)
- [ ] No unnecessary re-renders (stable references for callbacks, memoize expensive computations)
- [ ] Images use `next/image` with proper width/height (no CLS)
- [ ] Fonts loaded via `next/font` (no FOUT)
- [ ] No heavy animations on elements likely to appear in lists (n * animation = jank)
- [ ] Chart data limited to visible viewport (don't render 10,000 points off-screen)
- [ ] `tabular-nums` on numeric displays (prevents layout shift on value changes)

## 6. The Final Test

After all checklist items pass, answer these honestly:

- [ ] **Would I be embarrassed if a designer from Linear saw this?** If yes, fix it.
- [ ] **Does every element earn its pixels?** Remove anything that doesn't serve the operator.
- [ ] **Is this information-dense without being cluttered?** The difference is alignment, spacing, and hierarchy — not removing content.
- [ ] **Would this work at 3am during an inference outage?** Status should be instantly readable. Error states should be obvious. Critical info should be above the fold.

---

## Courier-Specific Checks (Infrastructure Dashboard)

- [ ] All numeric values use `tabular-nums` + monospace where appropriate
- [ ] Model names, IDs, and endpoints in monospace
- [ ] Status indicators use signal colors (success/warning/error), not just text
- [ ] Charts use the brand-anchored palette (`--chart-1` through `--chart-5`)
- [ ] API method badges color-coded (GET=success, POST=brand, PUT=warning, DELETE=error)
- [ ] Memory/capacity displays show thresholds visually (not just numbers)
- [ ] Configuration values are copy-to-clipboard enabled
- [ ] Secrets/API keys are masked by default with toggle reveal
