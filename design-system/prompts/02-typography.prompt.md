# Step 2: Typography — Type Scale & Font System

## Purpose

Define Courier's complete type system: font stacks, weight tokens, a compound type scale (size + weight + line-height + letter-spacing locked per step), numeric display features, and font loading verification. Typography is the primary hierarchy tool in a data-dense AI platform UI — get this right and the entire product reads cleanly.

## Dependencies

**Required before starting:**
- `src/design-system/BRAND-AUDIT.md` — font findings from Step 0
- `src/app/globals.css` (or `src/styles/globals.css`) — color system from Step 1 is already in place. Typography adds to this file; do NOT overwrite color tokens.

## Quality Context

Read these before doing any work:

```
skills/ui-engineering/SKILL.md
skills/ui-engineering/references/design-tokens.md   ← Type scale architecture reference
constitution/ENGINEERING-CONSTITUTION.md
```

The `design-tokens.md` reference describes compound type classes. Courier's system follows that same pattern — each class locks ALL typographic properties so developers never mix individual Tailwind text/font utilities.

## Font Inventory Check

Before defining tokens, verify what actually exists:

1. **Find font files**: Search for `.woff2`, `.woff`, `.ttf`, `.otf` files in the project (likely under `public/fonts/`, `src/fonts/`, or `src/assets/fonts/`)
2. **Check @font-face**: Look in globals.css or any CSS file for existing `@font-face` declarations
3. **Check next/font**: Look in layout.tsx for `next/font/local` or `next/font/google` imports
4. **Verify GeneralSans**: Confirm it exists, note which weights are available (400, 500, 600, 700?)
5. **Verify HubotSans**: Confirm it exists, note which weights are available (must have Black/900)
6. **Check for monospace**: Is JetBrains Mono or another monospace font included? If not, rely on system monospace stack

Document findings before proceeding. If fonts are missing, flag it but continue with the system definition.

## Implementation

### A. Font Stack Tokens

Add to globals.css:

```css
:root {
  --font-body: 'GeneralSans', system-ui, -apple-system, 'Segoe UI', sans-serif;
  --font-display: 'HubotSans', system-ui, -apple-system, 'Segoe UI', sans-serif;
  --font-mono: 'JetBrains Mono', 'Fira Code', 'Cascadia Code', ui-monospace, 'SFMono-Regular', monospace;
}
```

Register with Tailwind v4:

```css
@theme inline {
  --font-family-body: var(--font-body);
  --font-family-display: var(--font-display);
  --font-family-mono: var(--font-mono);
}
```

### B. Weight Tokens

```css
:root {
  --font-weight-normal: 400;
  --font-weight-medium: 500;
  --font-weight-semibold: 600;
  --font-weight-bold: 700;
  --font-weight-black: 900;  /* HubotSans display only */
}
```

### C. Compound Type Scale Classes

Each class is a sealed unit. Using `.type-lg` gives you everything — size, weight, line-height, letter-spacing, and font-family. Developers NEVER combine these with individual `text-sm`, `font-bold`, `leading-tight` utilities.

```css
/* ============================================
   COURIER TYPE SCALE — Compound Classes
   Rule: Each class locks ALL properties.
   Never mix with individual Tailwind text/font utilities.
   ============================================ */

.type-xs {
  font-family: var(--font-body);
  font-size: 11px;
  line-height: 16px;
  font-weight: 700;
  letter-spacing: 0.02em;
  /* Use: Labels, metadata badges, overline text, uppercase tags */
}

.type-sm {
  font-family: var(--font-body);
  font-size: 12px;
  line-height: 16px;
  font-weight: 500;
  letter-spacing: 0;
  /* Use: Secondary text, table cells, timestamps, helper text */
}

.type-base {
  font-family: var(--font-body);
  font-size: 13px;
  line-height: 20px;
  font-weight: 500;
  letter-spacing: 0;
  /* Use: Default body text, descriptions, form labels */
}

.type-md {
  font-family: var(--font-body);
  font-size: 14px;
  line-height: 20px;
  font-weight: 500;
  letter-spacing: 0;
  /* Use: Primary UI text, nav items, button labels, input values */
}

.type-lg {
  font-family: var(--font-body);
  font-size: 16px;
  line-height: 24px;
  font-weight: 600;
  letter-spacing: 0;
  /* Use: Section headers, card titles, sidebar group labels */
}

.type-xl {
  font-family: var(--font-body);
  font-size: 20px;
  line-height: 28px;
  font-weight: 600;
  letter-spacing: 0;
  /* Use: Page titles, dialog headers, panel titles */
}

.type-2xl {
  font-family: var(--font-display);
  font-size: 24px;
  line-height: 32px;
  font-weight: 600;
  letter-spacing: -0.01em;
  /* Use: Major section headings, feature titles */
}

.type-3xl {
  font-family: var(--font-display);
  font-size: 32px;
  line-height: 40px;
  font-weight: 900;
  letter-spacing: -0.02em;
  /* Use: Metric/stat values, dashboard numbers, hero stats */
}

.type-4xl {
  font-family: var(--font-display);
  font-size: 48px;
  line-height: 56px;
  font-weight: 900;
  letter-spacing: -0.02em;
  /* Use: Hero numbers, landing page headlines, splash metrics */
}
```

**Architecture decisions:**
- `type-xs` through `type-xl` use GeneralSans (body font) — these are UI text
- `type-2xl` through `type-4xl` use HubotSans (display font) — these are display/heading text
- The crossover at `type-2xl` (switching from body to display) creates clear typographic hierarchy
- `type-3xl` and `type-4xl` use weight 900 (Black) — HubotSans Black is the brand's display signature
- Negative letter-spacing on larger sizes compensates for optical looseness at scale

### D. Numeric Display Features

Courier is an AI model platform with data-dense UIs — download progress, model sizes, token counts, latency numbers. Numeric rendering must be precise.

```css
/* Tabular numerals — equal-width digits for aligned columns */
.tabular-nums {
  font-variant-numeric: tabular-nums;
  font-feature-settings: "tnum" 1, "lnum" 1;
}

/* Full numeric features for data displays */
[data-numeric] {
  font-variant-numeric: tabular-nums lining-nums;
  font-feature-settings: "case" 1, "cv01" 1, "lnum" 1, "tnum" 1, "zero" 1;
}

/* Slashed zero — disambiguate 0 from O in model names, hashes */
.slashed-zero {
  font-feature-settings: "zero" 1;
}
```

Usage guidance:
- Apply `.tabular-nums` to any column of numbers (tables, stats grids, progress indicators)
- Apply `data-numeric` attribute to stat cards, metric displays, model parameter counts
- Apply `.slashed-zero` to model identifiers, hash displays, version numbers

### E. Font Loading

Verify and fix font loading. The approach depends on what currently exists:

**If using `next/font/local`** (preferred for self-hosted WOFF2):

Check that `src/app/layout.tsx` has imports like:

```tsx
import localFont from 'next/font/local'

const generalSans = localFont({
  src: [
    { path: './fonts/GeneralSans-Medium.woff2', weight: '500', style: 'normal' },
    { path: './fonts/GeneralSans-Semibold.woff2', weight: '600', style: 'normal' },
    // ... other weights
  ],
  variable: '--font-body',
  display: 'swap',
})

const hubotSans = localFont({
  src: [
    { path: './fonts/HubotSans-Bold.woff2', weight: '700', style: 'normal' },
    { path: './fonts/HubotSans-Black.woff2', weight: '900', style: 'normal' },
    // ... other weights
  ],
  variable: '--font-display',
  display: 'swap',
})
```

And the `<html>` tag applies both variables:
```tsx
<html className={`${generalSans.variable} ${hubotSans.variable}`}>
```

**If using `@font-face` in CSS**: Ensure declarations exist for all needed weights, `font-display: swap` is set, and WOFF2 paths resolve correctly.

**If fonts are partially configured**: Fix whatever is broken. The system depends on both fonts loading correctly.

**Subsetting**: If the WOFF2 files are large (>100KB each), note this as a future optimization opportunity. Don't block on it now, but document it in the typography docs.

### F. Tailwind v4 Integration

The compound classes are pure CSS (defined in globals.css), not Tailwind utilities. This is intentional — they are semantic design decisions, not compositional utilities.

However, register the font families with `@theme inline` so that `font-body`, `font-display`, `font-mono` work as Tailwind utilities for the rare cases where a developer needs just the font-family without the full compound class.

## File Outputs

### 1. Update `src/app/globals.css` (or `src/styles/globals.css`)

Add the typography section AFTER the color system. Use clear section comments:

```css
/* ============================================
   COURIER DESIGN SYSTEM — Typography
   Generated: Step 2 of design system pipeline
   ============================================ */

/* --- Font Stacks --- */
/* ... font-body, font-display, font-mono ... */

/* --- Weight Tokens --- */
/* ... weight variables ... */

/* --- Type Scale (compound classes) --- */
/* ... .type-xs through .type-4xl ... */

/* --- Numeric Features --- */
/* ... .tabular-nums, [data-numeric], .slashed-zero ... */
```

Do NOT touch the color tokens from Step 1. Append typography below them.

### 2. Create `src/design-system/docs/typography.md`

```markdown
# Courier Typography System

## Font Stacks
| Token | Value | Usage |
|-------|-------|-------|
| --font-body | 'GeneralSans', system-ui, ... | All UI text (type-xs through type-xl) |
| --font-display | 'HubotSans', system-ui, ... | Display text (type-2xl through type-4xl) |
| --font-mono | 'JetBrains Mono', ... | Code, model names, terminal output |

## Type Scale
| Class | Size/Line | Weight | Tracking | Font | Usage |
|-------|-----------|--------|----------|------|-------|
| .type-xs | 11/16 | 700 | 0.02em | Body | Labels, metadata, tags |
| .type-sm | 12/16 | 500 | 0 | Body | Secondary text, timestamps |
| .type-base | 13/20 | 500 | 0 | Body | Body text, descriptions |
| .type-md | 14/20 | 500 | 0 | Body | Primary UI text, buttons |
| .type-lg | 16/24 | 600 | 0 | Body | Section headers, card titles |
| .type-xl | 20/28 | 600 | 0 | Body | Page titles, dialog headers |
| .type-2xl | 24/32 | 600 | -0.01em | Display | Major headings |
| .type-3xl | 32/40 | 900 | -0.02em | Display | Metric values, stats |
| .type-4xl | 48/56 | 900 | -0.02em | Display | Hero numbers, splash |

## Rules
1. **Never mix type classes with individual Tailwind text/font utilities.**
   Wrong: `<h2 className="type-lg font-bold text-sm">`
   Right: `<h2 className="type-lg">`

2. **Use the right class for the right role** — don't pick by visual size.
   If it's a card title, use `type-lg` even if you wish it were bigger.

3. **Display font (HubotSans) is only for type-2xl and above.**
   Never apply `font-display` to body-sized text.

4. **Add `data-numeric` to stat displays.**
   `<span data-numeric>{value}</span>` activates tabular nums + OpenType features.

## Font Loading
[Document the actual loading mechanism found/configured]
[Note any subsetting opportunities]
```

## Verification Steps

1. **Dev server**: Run `pnpm dev`, confirm no font loading errors in console
2. **Visual check**: Open the app — body text should be GeneralSans, any large headings HubotSans
3. **Type scale test**: Temporarily render all 9 type classes on a test page to verify visual hierarchy
4. **Numeric test**: Render a column of numbers with `.tabular-nums` — digits should align vertically
5. **Tailwind check**: `font-body`, `font-display`, `font-mono` utilities should work
6. **No regressions**: Existing components should not have broken typography

## Definition of Done

- [ ] Font stacks defined as CSS variables and registered with @theme inline
- [ ] All 9 compound type scale classes (.type-xs through .type-4xl) exist in globals.css
- [ ] Each class locks font-family, font-size, line-height, font-weight, and letter-spacing
- [ ] Numeric display features (.tabular-nums, [data-numeric], .slashed-zero) are defined
- [ ] GeneralSans loads correctly (verify in browser DevTools → Fonts tab)
- [ ] HubotSans loads correctly with Black (900) weight available
- [ ] Font loading uses `font-display: swap` for performance
- [ ] `src/design-system/docs/typography.md` documents every class and its intended usage
- [ ] Color tokens from Step 1 are untouched — typography is additive only
- [ ] No TypeScript or build errors
