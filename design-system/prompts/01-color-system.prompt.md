# Step 1: Color System — Rationalized Token Architecture

## Purpose

Take the raw brand audit from Step 0 and build a rationalized, OKLCH-based color token system. This replaces scattered color values with a unified architecture that preserves Courier's visual identity — deep slate backgrounds, cyan/teal primary, amber/orange secondary — while making every color decision intentional and tokenized.

## Dependencies

**Required before starting:**
- `src/design-system/BRAND-AUDIT.md` — output from Step 0. Read this first. Every color decision here must trace back to what the audit found.

## Quality Context

Read these before doing any work:

```
skills/ui-engineering/SKILL.md
skills/ui-engineering/references/design-tokens.md   ← PRIMARY architecture reference
constitution/ENGINEERING-CONSTITUTION.md
```

The `design-tokens.md` reference defines the token architecture pattern (gray scale, alpha text, accent variants, signal colors, chart palette, shadcn bridge). Follow that structure exactly.

## Reference Architecture

The design-tokens reference describes the Athlete Era pattern. Read it carefully — Courier's system should follow the same architecture with Courier's own color identity.

Key patterns to replicate:
- 12-step gray scale in OKLCH with subtle cool hue
- Alpha-based text hierarchy (--t1, --t2, --t3)
- Accent colors with solid + hover + alpha variants
- Signal colors with alpha backgrounds for badges
- Chart palette via OKLCH hue rotation
- shadcn compatibility layer mapping rich tokens to expected variables

## Implementation

### A. Gray Scale — 12-Step Cool Slate (OKLCH)

Build a 12-step scale from near-black to near-white. Courier's identity is deep, cool-toned darkness.

```css
/* Target feel: deep slate/charcoal, NOT pure neutral gray */
/* Hue: ~250 (blue-slate), subtle chroma in darker steps, desaturating toward lighter steps */

@theme inline {
  --color-gray-1: oklch(0.13 0.015 250);   /* Deepest bg — app background */
  --color-gray-2: oklch(0.155 0.014 250);  /* Card/surface bg */
  --color-gray-3: oklch(0.18 0.013 250);   /* Elevated surface, hover bg */
  --color-gray-4: oklch(0.21 0.012 250);   /* Active/pressed bg */
  --color-gray-5: oklch(0.25 0.010 250);   /* Subtle border */
  --color-gray-6: oklch(0.30 0.008 250);   /* Default border */
  --color-gray-7: oklch(0.36 0.006 250);   /* Strong border, divider */
  --color-gray-8: oklch(0.44 0.005 250);   /* Placeholder text, disabled */
  --color-gray-9: oklch(0.55 0.003 250);   /* Tertiary text */
  --color-gray-10: oklch(0.68 0.002 250);  /* Secondary text */
  --color-gray-11: oklch(0.80 0.001 250);  /* Secondary text (brighter) */
  --color-gray-12: oklch(0.93 0.000 0);    /* Primary text */
}
```

**IMPORTANT:** These are starting points. Tune each step by:
1. Checking the audit for Courier's actual background/border/text colors
2. Ensuring sufficient contrast between adjacent steps
3. Verifying WCAG AA contrast for text steps (9-12) against bg steps (1-3)
4. Testing in the actual UI — render components and verify visual hierarchy

### B. Alpha-Based Text System

```css
:root {
  --t1: oklch(1 0 0 / 0.92);    /* Primary text — headings, body */
  --t2: oklch(1 0 0 / 0.56);    /* Secondary text — labels, metadata */
  --t3: oklch(1 0 0 / 0.34);    /* Tertiary text — placeholders, disabled */
}
```

Also create aliases:
- `--gray-12` → aliased to the same lightness as `--t1`
- `--gray-11` → aliased to the same lightness as `--t2`

The alpha system is for overlays and adaptive contexts. The gray scale is for solid backgrounds. Both should produce visually identical results on the default background.

### C. Brand Accent — Cyan/Teal

Courier's primary identity color. Preserve the existing cyan feel from the audit.

```css
@theme inline {
  --color-brand: oklch(0.72 0.15 195);           /* Primary cyan — buttons, links, active */
  --color-brand-hover: oklch(0.78 0.13 195);     /* Hover state — lighter, slightly less saturated */
  --color-brand-alpha-10: oklch(0.72 0.15 195 / 0.10);  /* Subtle tint — tag bg, hover highlight */
  --color-brand-alpha-20: oklch(0.72 0.15 195 / 0.20);  /* Stronger tint — active bg, focus ring */
}
```

**Verify against audit:** Check what cyan/teal values Courier currently uses. The hue should match the existing identity. If the audit shows `hsl(180, ...)` or a specific hex, convert to OKLCH and anchor the hue there.

### D. Secondary Accent — Amber/Orange

Used for badges, warnings-that-aren't-errors, secondary CTAs, status indicators.

```css
@theme inline {
  --color-secondary: oklch(0.75 0.16 70);          /* Amber/orange solid */
  --color-secondary-hover: oklch(0.80 0.14 70);    /* Hover state */
  --color-secondary-alpha-10: oklch(0.75 0.16 70 / 0.10);  /* Subtle tint */
  --color-secondary-alpha-20: oklch(0.75 0.16 70 / 0.20);  /* Stronger tint */
}
```

### E. Signal / Status Colors

Each status color needs: solid, and 10% alpha variant (for badge/tag backgrounds).

```css
@theme inline {
  /* Success — emerald */
  --color-success: oklch(0.72 0.17 155);
  --color-success-alpha-10: oklch(0.72 0.17 155 / 0.10);

  /* Warning — amber (can share hue with secondary, adjust lightness) */
  --color-warning: oklch(0.80 0.16 85);
  --color-warning-alpha-10: oklch(0.80 0.16 85 / 0.10);

  /* Error — red */
  --color-error: oklch(0.65 0.20 25);
  --color-error-alpha-10: oklch(0.65 0.20 25 / 0.10);

  /* Info — brand cyan (reuse brand) */
  --color-info: var(--color-brand);
  --color-info-alpha-10: var(--color-brand-alpha-10);
}
```

### F. Chart Palette

5-6 colors for data visualization. Use OKLCH hue rotation anchored to brand cyan for perceptual uniformity.

```css
@theme inline {
  --color-chart-1: oklch(0.72 0.15 195);  /* Brand cyan (anchor) */
  --color-chart-2: oklch(0.72 0.15 255);  /* Blue-violet */
  --color-chart-3: oklch(0.72 0.15 315);  /* Magenta-pink */
  --color-chart-4: oklch(0.72 0.15 45);   /* Orange-gold */
  --color-chart-5: oklch(0.72 0.15 135);  /* Green */
  --color-chart-6: oklch(0.72 0.15 15);   /* Red-orange */
}
```

All chart colors share the same lightness (0.72) and chroma (0.15) — only hue rotates. This guarantees equal visual weight in charts and graphs.

### G. shadcn Compatibility Layer

shadcn components expect specific CSS variable names. Map Courier's richer system to these expected variables so all shadcn components render correctly without modification.

```css
:root {
  /* Page */
  --background: var(--color-gray-1);
  --foreground: var(--color-gray-12);

  /* Cards & surfaces */
  --card: var(--color-gray-2);
  --card-foreground: var(--color-gray-12);
  --popover: var(--color-gray-2);
  --popover-foreground: var(--color-gray-12);

  /* Primary action */
  --primary: var(--color-brand);
  --primary-foreground: oklch(0.13 0.015 250);  /* Dark text on cyan */

  /* Secondary action */
  --secondary: var(--color-gray-3);
  --secondary-foreground: var(--color-gray-12);

  /* Muted / subtle */
  --muted: var(--color-gray-3);
  --muted-foreground: var(--color-gray-9);

  /* Accent (hover highlights) */
  --accent: var(--color-gray-3);
  --accent-foreground: var(--color-gray-12);

  /* Destructive */
  --destructive: var(--color-error);
  --destructive-foreground: var(--color-gray-12);

  /* Borders & inputs */
  --border: var(--color-gray-5);
  --input: var(--color-gray-5);
  --ring: var(--color-brand-alpha-20);

  /* Radius */
  --radius: 0.5rem;

  /* Sidebar (if using shadcn sidebar) */
  --sidebar: var(--color-gray-2);
  --sidebar-foreground: var(--color-gray-11);
  --sidebar-primary: var(--color-brand);
  --sidebar-primary-foreground: oklch(0.13 0.015 250);
  --sidebar-accent: var(--color-gray-3);
  --sidebar-accent-foreground: var(--color-gray-12);
  --sidebar-border: var(--color-gray-5);
  --sidebar-ring: var(--color-brand-alpha-20);

  /* Chart */
  --chart-1: var(--color-chart-1);
  --chart-2: var(--color-chart-2);
  --chart-3: var(--color-chart-3);
  --chart-4: var(--color-chart-4);
  --chart-5: var(--color-chart-5);
}
```

### H. Tailwind v4 @theme Inline Bridge

All CSS variables defined in sections A-F should be declared inside `@theme inline { }` blocks so Tailwind v4 generates utility classes automatically.

This means `--color-brand` becomes usable as `bg-brand`, `text-brand`, `border-brand` etc. without any additional config.

Verify this works by checking that Tailwind v4's CSS-first configuration picks up the variables. The `@theme inline` directive is the mechanism — no `tailwind.config.ts` needed for colors.

## File Outputs

### 1. Update `src/app/globals.css` (or `src/styles/globals.css`)

Replace the existing color section with the complete rationalized system. Structure the file as:

```css
/* ============================================
   COURIER DESIGN SYSTEM — Color Tokens
   Generated: Step 1 of design system pipeline
   ============================================ */

/* --- Gray Scale (12-step, OKLCH, cool-slate) --- */
@theme inline {
  /* ... gray-1 through gray-12 ... */
}

/* --- Text Hierarchy (alpha-based) --- */
/* ... --t1, --t2, --t3 ... */

/* --- Brand Accent (cyan/teal) --- */
@theme inline {
  /* ... brand variants ... */
}

/* --- Secondary Accent (amber/orange) --- */
@theme inline {
  /* ... secondary variants ... */
}

/* --- Signal Colors --- */
@theme inline {
  /* ... success, warning, error, info ... */
}

/* --- Chart Palette --- */
@theme inline {
  /* ... chart-1 through chart-6 ... */
}

/* --- shadcn Compatibility Layer --- */
:root {
  /* ... all shadcn expected variables ... */
}

/* ... rest of globals.css (resets, base styles, etc.) ... */
```

**CRITICAL:** Do not delete existing non-color CSS in globals.css. Only replace/add the color section. Preserve resets, base styles, font-face declarations, and any other non-color rules.

### 2. Create `src/design-system/docs/colors.md`

Document every token:

```markdown
# Courier Color System

## Gray Scale
| Token | Value | Usage |
|-------|-------|-------|
| --color-gray-1 | oklch(...) | App background, deepest layer |
| ... | ... | ... |

## Text Hierarchy
| Token | Value | Usage |
|-------|-------|-------|
| --t1 | oklch(1 0 0 / 0.92) | Primary text, headings, body copy |
| ... | ... | ... |

## Brand Accent
[same pattern]

## Secondary Accent
[same pattern]

## Signal Colors
[same pattern]

## Chart Palette
[same pattern]

## shadcn Mapping
| shadcn Variable | Maps To | Notes |
|----------------|---------|-------|
| --background | --color-gray-1 | Page background |
| ... | ... | ... |

## Usage Guidelines
- Always use semantic tokens, never raw OKLCH values in components
- For new colors: add to this system first, then use the token
- Border-based hierarchy (dark mode): use gray-5/6/7 borders, not shadows
- Brand cyan is the primary interactive color — buttons, links, active states
- Amber is secondary — badges, non-critical status, supplementary actions
```

## Verification Steps

After implementation, verify:

1. **Run the dev server** (`pnpm dev`) and confirm the app renders without color regressions
2. **Check shadcn components**: Button, Card, Input, Badge, Dialog should all render correctly
3. **Check contrast**: Primary text (--t1) on app bg (gray-1) meets WCAG AA (4.5:1 minimum)
4. **Check Tailwind utilities**: `bg-brand`, `text-gray-12`, `border-gray-5` should all work
5. **Spot check components**: Open 3-4 existing components and verify colors didn't break

## Definition of Done

- [ ] globals.css contains the complete color system (gray scale, text hierarchy, brand, secondary, signals, chart, shadcn bridge)
- [ ] All colors use OKLCH format
- [ ] @theme inline blocks enable Tailwind v4 utility generation
- [ ] shadcn components render correctly (no broken/missing colors)
- [ ] `src/design-system/docs/colors.md` documents every token with its value and usage
- [ ] No raw hex/hsl color values remain in globals.css (all converted to OKLCH)
- [ ] Courier's visual identity is preserved — the app should LOOK the same, just with rationalized tokens
- [ ] The dev server runs without errors
