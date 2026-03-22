# Courier Design Token Architecture

> Reference-quality token spec for Courier's dark-first AI infrastructure dashboard.
> Modeled after Athlete Era's layered approach, adapted for Courier's cold-tech identity.

**Stack:** Tailwind v4 (OKLCH) | shadcn/ui | GeneralSans + HubotSans Black

---

## 1. Color Token Architecture

### 12-Step Gray Scale (Cool Slate Undertone, OKLCH)

Hue ~250 gives Courier its cold, technical personality. Every surface, border, and text value derives from this scale.

```css
:root {
  /* ── Gray Scale ─────────────────────────────────────── */
  --gray-1:  oklch(0.14 0.005 250);   /* Deepest background — app shell, sidebars */
  --gray-2:  oklch(0.17 0.005 250);   /* Sunken surfaces — code blocks, wells */
  --gray-3:  oklch(0.21 0.005 250);   /* Default background — main content */
  --gray-4:  oklch(0.25 0.004 250);   /* Elevated surfaces — cards, panels */
  --gray-5:  oklch(0.29 0.004 250);   /* Higher elevation — nested cards, popovers */
  --gray-6:  oklch(0.33 0.004 250);   /* Subtle borders — dividers, separators */
  --gray-7:  oklch(0.40 0.003 250);   /* Default borders — card edges, inputs */
  --gray-8:  oklch(0.48 0.003 250);   /* Strong borders — focused inputs, emphasis */
  --gray-9:  oklch(0.56 0.002 250);   /* Placeholder text — disabled, hints */
  --gray-10: oklch(0.64 0.002 250);   /* Tertiary text — timestamps, metadata */
  --gray-11: oklch(0.74 0.001 250);   /* Secondary text — descriptions, labels */
  --gray-12: oklch(0.90 0.000 0);     /* Primary text — headings, body copy */
}
```

**Usage rules:**
- Gray 1-5 are surfaces (backgrounds). Never use for text.
- Gray 6-8 are borders/dividers. Can double as disabled states.
- Gray 9-12 are text/icon values. Never use for backgrounds.
- Jumps of 2+ steps between adjacent surfaces ensure contrast.

### Alpha-Based Text Tokens

White-on-dark alpha tokens for text that naturally adapts to any surface behind it.

```css
:root {
  --t1: oklch(1.00 0 0 / 0.92);   /* Primary — headings, body, interactive labels */
  --t2: oklch(1.00 0 0 / 0.56);   /* Secondary — descriptions, supporting copy */
  --t3: oklch(1.00 0 0 / 0.34);   /* Tertiary — placeholders, timestamps, hints */
}
```

**When to use alpha vs. gray-scale text:**
- Use `--t1`/`--t2`/`--t3` when text sits on variable-color surfaces (e.g., brand-tinted cards, overlays).
- Use `--gray-10`/`--gray-11`/`--gray-12` when text sits on predictable gray surfaces (the common case).
- Alpha tokens are especially useful for text over gradients or semi-transparent panels.

### Brand Accent (Cyan/Teal)

The primary signature color. Cyan conveys speed, precision, and digital clarity.

```css
:root {
  /* ── Brand ──────────────────────────────────────────── */
  --brand:         oklch(0.75 0.14 195);   /* Primary cyan — CTAs, links, active states */
  --brand-hover:   oklch(0.82 0.12 195);   /* Lighter cyan — hover state */
  --brand-active:  oklch(0.68 0.15 195);   /* Pressed/active state */
  --brand-10:      oklch(0.75 0.14 195 / 0.10);  /* 10% — subtle backgrounds, tinted rows */
  --brand-20:      oklch(0.75 0.14 195 / 0.20);  /* 20% — selection highlight, focus rings */
}
```

### Secondary Accent (Amber/Orange)

Amber signals attention without alarm. Used for badges, warnings, and secondary emphasis.

```css
:root {
  /* ── Amber ──────────────────────────────────────────── */
  --accent-amber:     oklch(0.80 0.15 75);    /* Secondary amber — badges, callouts */
  --accent-amber-hover: oklch(0.85 0.13 75);  /* Hover */
  --accent-amber-10:  oklch(0.80 0.15 75 / 0.10);  /* 10% — background tint */
  --accent-amber-20:  oklch(0.80 0.15 75 / 0.20);  /* 20% — stronger tint */
}
```

### Signal / Status Colors

Infrastructure naming. These are system states, not market sentiments.

```css
:root {
  /* ── Signals ────────────────────────────────────────── */
  --success:      #10B981;                         /* Emerald — healthy, connected, complete */
  --success-10:   oklch(0.70 0.15 160 / 0.10);    /* Background tint */

  --warning:      #F59E0B;                         /* Amber — degraded, approaching limit */
  --warning-10:   oklch(0.80 0.15 80 / 0.10);     /* Background tint */

  --error:        #EF4444;                         /* Red — failed, disconnected, critical */
  --error-10:     oklch(0.60 0.20 25 / 0.10);     /* Background tint */

  --info:         var(--brand);                    /* Cyan — informational, neutral alert */
  --info-10:      var(--brand-10);                 /* Background tint */
}
```

**Pattern for signal usage:**
- Solid color for icons, badges, status dots.
- `-10` variant for background fills (e.g., alert banners, table row highlights).
- Never use signal colors for decorative purposes — they carry semantic meaning.

### Chart Colors (OKLCH Hue Rotation)

Five-color palette built from complementary hue rotation around the cyan anchor. Designed for equal perceptual weight in dark mode.

```css
:root {
  /* ── Charts ─────────────────────────────────────────── */
  --chart-1: var(--brand);            /* Cyan — primary series */
  --chart-2: oklch(0.72 0.14 180);    /* Teal — secondary series */
  --chart-3: oklch(0.72 0.14 290);    /* Violet — tertiary series */
  --chart-4: oklch(0.75 0.12 60);     /* Amber — quaternary series */
  --chart-5: oklch(0.70 0.14 330);    /* Rose — quinary series */
}
```

**Rules:**
- Always use in order (1→5). Don't cherry-pick colors for aesthetics.
- If more than 5 series are needed, add alpha variants of existing colors before introducing new hues.
- Ensure each chart has a legend — don't rely on color alone for meaning.

---

## 2. Naming Conventions

### Three-Layer Architecture (Colors)

```
Primitive        →  Semantic         →  Component
─────────────────────────────────────────────────
--gray-4             --bg-elevated        --card-bg
--brand              --action-primary     --btn-primary-bg
--error              --signal-error       --toast-error-bg
```

**Primitive:** Raw values. Defined once, never used directly in components.
**Semantic:** Purpose-driven aliases. The layer developers actually write.
**Component:** Scoped overrides for complex components. Optional — only create when semantic tokens create ambiguity.

### Two-Layer Architecture (Spacing, Typography, Radius)

```
Semantic         →  Component
─────────────────────────────
--space-4            --card-padding
--text-sm            --badge-font-size
--radius-md          --card-radius
```

No primitive layer needed — spacing/type scales are already semantic by nature.

### shadcn/ui Compatibility Layer

Map Courier's semantic tokens to shadcn's expected variable names. This keeps shadcn components working without forking their internals.

```css
:root {
  /* ── shadcn bridge ──────────────────────────────────── */
  --background:         var(--bg);
  --foreground:         var(--t1);
  --card:               var(--bg-elevated);
  --card-foreground:    var(--gray-12);
  --popover:            var(--bg-elevated);
  --popover-foreground: var(--gray-12);
  --primary:            var(--brand);
  --primary-foreground: oklch(0.15 0.005 250);  /* Dark text on cyan */
  --secondary:          var(--gray-4);
  --secondary-foreground: var(--gray-12);
  --muted:              var(--gray-3);
  --muted-foreground:   var(--t2);
  --accent:             var(--gray-4);
  --accent-foreground:  var(--gray-12);
  --destructive:        var(--error);
  --destructive-foreground: oklch(1.00 0 0 / 0.92);
  --border:             var(--border-default);
  --input:              var(--border-default);
  --ring:               var(--brand);
  --chart-1:            var(--chart-1);
  --chart-2:            var(--chart-2);
  --chart-3:            var(--chart-3);
  --chart-4:            var(--chart-4);
  --chart-5:            var(--chart-5);
}
```

---

## 3. Tailwind v4 @theme inline Bridge

Tailwind v4 uses `@theme inline` to register CSS variables as first-class utilities. This is the bridge between your CSS custom properties and Tailwind classes.

```css
@theme inline {
  /* ── Colors ─────────────────────────────────────────── */
  --color-gray-1:  var(--gray-1);
  --color-gray-2:  var(--gray-2);
  --color-gray-3:  var(--gray-3);
  --color-gray-4:  var(--gray-4);
  --color-gray-5:  var(--gray-5);
  --color-gray-6:  var(--gray-6);
  --color-gray-7:  var(--gray-7);
  --color-gray-8:  var(--gray-8);
  --color-gray-9:  var(--gray-9);
  --color-gray-10: var(--gray-10);
  --color-gray-11: var(--gray-11);
  --color-gray-12: var(--gray-12);

  --color-t1: var(--t1);
  --color-t2: var(--t2);
  --color-t3: var(--t3);

  --color-brand:       var(--brand);
  --color-brand-hover: var(--brand-hover);
  --color-brand-10:    var(--brand-10);
  --color-brand-20:    var(--brand-20);

  --color-amber:    var(--accent-amber);
  --color-amber-10: var(--accent-amber-10);

  --color-success:    var(--success);
  --color-success-10: var(--success-10);
  --color-warning:    var(--warning);
  --color-warning-10: var(--warning-10);
  --color-error:      var(--error);
  --color-error-10:   var(--error-10);

  /* ── Shadows ────────────────────────────────────────── */
  --shadow-sm:  var(--shadow-sm);
  --shadow-md:  var(--shadow-md);
  --shadow-lg:  var(--shadow-lg);

  /* ── Radius ─────────────────────────────────────────── */
  --radius-sm:   var(--radius-sm);
  --radius-md:   var(--radius-md);
  --radius-lg:   var(--radius-lg);
  --radius-card: var(--radius-card);
  --radius-pill: var(--radius-pill);
}
```

**This enables classes like:**
```html
<div class="bg-gray-3 text-t1 border-brand rounded-card shadow-sm">
  <p class="text-t2">Secondary description</p>
  <span class="bg-success-10 text-success">Healthy</span>
</div>
```

---

## 4. Spacing Scale (4px Grid)

Every spacing value is a multiple of 4px. No exceptions.

```css
:root {
  /* ── Spacing primitives ─────────────────────────────── */
  --space-0:   0px;
  --space-0.5: 2px;      /* Exception: micro-adjustments only (icon nudges) */
  --space-1:   4px;
  --space-2:   8px;
  --space-3:   12px;
  --space-4:   16px;
  --space-5:   20px;
  --space-6:   24px;
  --space-7:   28px;
  --space-8:   32px;
  --space-10:  40px;
  --space-12:  48px;
  --space-14:  56px;
  --space-16:  64px;
  --space-20:  80px;

  /* ── Semantic spacing ───────────────────────────────── */
  --card-padding:   var(--space-5);    /* 20px — internal card padding */
  --card-gap:       var(--space-4);    /* 16px — between items within a card */
  --section-gap:    var(--space-8);    /* 32px — between major sections */
  --page-padding:   var(--space-6);    /* 24px — page-level inline padding */
  --page-gap:       var(--space-10);   /* 40px — between page-level blocks */
  --input-padding-x: var(--space-3);   /* 12px — horizontal input padding */
  --input-padding-y: var(--space-2);   /* 8px  — vertical input padding */
  --stack-gap:      var(--space-2);    /* 8px  — tight vertical stack (form fields) */
}
```

**Decision guide:**
| Context | Token | Value |
|---------|-------|-------|
| Icon to label | `--space-2` | 8px |
| Form field to next field | `--stack-gap` | 8px |
| Card internal content | `--card-gap` | 16px |
| Card body padding | `--card-padding` | 20px |
| Section to section | `--section-gap` | 32px |
| Page block to block | `--page-gap` | 40px |

---

## 5. Radius Scale

```css
:root {
  /* ── Radius ─────────────────────────────────────────── */
  --radius-none: 0px;       /* Tables, code blocks, terminal output */
  --radius-sm:   4px;       /* Inputs, buttons, badges, tooltips */
  --radius-md:   8px;       /* Cards, panels, dialogs */
  --radius-lg:   12px;      /* Large cards, modals, hero sections */
  --radius-card: 12px;      /* Alias — card-specific (same as lg) */
  --radius-pill: 9999px;    /* Status dots, toggle tracks, tags */
}
```

**Infrastructure dashboard conventions:**
- `--radius-md` for cards and elevated panels.
- `--radius-sm` for inputs, selects, and small interactive elements.
- `--radius-none` for tables, code blocks, terminal output, and log viewers. Sharp edges signal "raw data."
- `--radius-pill` for status indicators, count badges, and toggle switches.

---

## 6. Shadow Scale (Dark-Optimized)

Dark UIs rely on **surface color shifts** for elevation, not shadows. Shadows are supplementary — used only when layering demands explicit depth separation (popovers, dropdowns, modals).

```css
:root {
  /* ── Shadows ────────────────────────────────────────── */
  --shadow-sm:
    0 1px 2px oklch(0 0 0 / 0.30),
    0 1px 3px oklch(0 0 0 / 0.15);

  --shadow-md:
    0 4px 6px oklch(0 0 0 / 0.35),
    0 2px 4px oklch(0 0 0 / 0.20);

  --shadow-lg:
    0 10px 15px oklch(0 0 0 / 0.40),
    0 4px 6px oklch(0 0 0 / 0.25);

  /* ── Focus ring ─────────────────────────────────────── */
  --shadow-focus:
    0 0 0 2px var(--bg),
    0 0 0 4px var(--brand);
}
```

**Usage rules:**
- Cards on `--bg`: No shadow needed. Surface color (`--gray-4` on `--gray-3`) provides the elevation cue.
- Popovers/dropdowns: Use `--shadow-md`.
- Modals/dialogs: Use `--shadow-lg`.
- `--shadow-focus` creates a double-ring: an inner ring matching the background (gap) and an outer brand-colored ring. Apply on `:focus-visible`.

---

## 7. Surface System

Four elevation levels. In dark mode, **lighter = more elevated**. This is the inverse of light mode conventions.

```css
:root {
  /* ── Surfaces ───────────────────────────────────────── */
  --bg:          var(--gray-3);   /* Default page background */
  --bg-sunken:   var(--gray-2);   /* Recessed areas — code wells, inset panels */
  --bg-deep:     var(--gray-1);   /* Deepest — app shell, sidebar chrome */
  --bg-elevated: var(--gray-4);   /* Raised surfaces — cards, panels, sheets */
  --bg-overlay:  var(--gray-5);   /* Highest — popovers, dropdowns, tooltips */
}
```

**Elevation stacking order:**
```
bg-deep (sidebar/shell)
  └─ bg (page content)
       └─ bg-elevated (cards)
            └─ bg-overlay (popovers/tooltips)
```

**Rules:**
- Never place a card (`--bg-elevated`) directly on `--bg-deep` — the contrast jump is too large. Use `--bg` as the intermediate layer.
- Nested cards: outer card = `--bg-elevated`, inner card = `--bg-overlay` (or use a subtle border instead of elevation).
- Overlays (modals) should use `--bg-elevated` with `--shadow-lg`, not a separate surface token.

---

## 8. Border System (Alpha-Based)

Alpha-based borders adapt to any surface automatically. No need for separate border tokens per elevation level.

```css
:root {
  /* ── Borders ────────────────────────────────────────── */
  --border-default: oklch(1 0 0 / 0.06);   /* Standard — card edges, dividers */
  --border-mid:     oklch(1 0 0 / 0.12);   /* Emphasized — section separators */
  --border-strong:  oklch(1 0 0 / 0.22);   /* High emphasis — focused inputs, active states */
}
```

**Usage:**
- Card borders: `--border-default` (barely visible, just enough to define edges).
- Table row dividers: `--border-default`.
- Input borders (resting): `--border-mid`.
- Input borders (focused): `--border-strong` or switch to `--brand` for brand-colored focus.
- Sidebar/panel separators: `--border-default`.

---

## 9. Courier-Specific Tokens

Tokens unique to infrastructure dashboards that don't exist in typical design systems.

### Capacity / Resource Meters

For memory gauges, CPU bars, token budgets, and storage meters.

```css
:root {
  /* ── Capacity ───────────────────────────────────────── */
  --capacity-safe:     var(--success);      /* 0-70% — normal operation */
  --capacity-warn:     var(--warning);      /* 70-90% — approaching limit */
  --capacity-critical: var(--error);        /* 90-100% — at or over limit */

  --capacity-safe-bg:     var(--success-10);
  --capacity-warn-bg:     var(--warning-10);
  --capacity-critical-bg: var(--error-10);
}
```

### Code / Terminal Tokens

```css
:root {
  /* ── Code ───────────────────────────────────────────── */
  --code-bg:     var(--gray-2);
  --code-border: var(--border-default);
  --code-font:   var(--font-mono);
  --code-size:   0.8125rem;     /* 13px — optimized for monospace legibility */

  --font-mono:   'JetBrains Mono', 'Fira Code', 'SF Mono', 'Cascadia Code',
                 ui-monospace, SFMono-Regular, Consolas, 'Liberation Mono', monospace;
}
```

### API Method Colors

Consistent color coding for HTTP methods across API explorers, logs, and documentation.

```css
:root {
  /* ── API Methods ────────────────────────────────────── */
  --method-get:    oklch(0.72 0.15 155);    /* Green — safe, retrieval */
  --method-post:   oklch(0.72 0.14 240);    /* Blue — creation */
  --method-put:    oklch(0.76 0.13 70);     /* Amber — modification */
  --method-patch:  oklch(0.76 0.13 70);     /* Amber — partial modification */
  --method-delete: oklch(0.65 0.18 25);     /* Red — destruction */

  /* Background tints for method badges */
  --method-get-bg:    oklch(0.72 0.15 155 / 0.10);
  --method-post-bg:   oklch(0.72 0.14 240 / 0.10);
  --method-put-bg:    oklch(0.76 0.13 70 / 0.10);
  --method-patch-bg:  oklch(0.76 0.13 70 / 0.10);
  --method-delete-bg: oklch(0.65 0.18 25 / 0.10);
}
```

### Connection State Tokens

For WebSocket indicators, service health, agent status, and pipeline states.

```css
:root {
  /* ── Connection States ──────────────────────────────── */
  --state-connected:    var(--success);       /* Live, healthy */
  --state-connecting:   var(--warning);       /* In progress, handshake */
  --state-disconnected: var(--gray-9);        /* Offline, idle */
  --state-error:        var(--error);         /* Failed, unreachable */

  /* Pulsing dot for "connecting" uses --state-connecting with CSS animation */
  /* Solid dot for "connected" uses --state-connected, no animation */
  /* Dim dot for "disconnected" uses --state-disconnected, reduced opacity */
}
```

---

## 10. Color Palette Direction

### This Is Courier, Sharpened

The cyan glow. The amber badges. The cool slate surfaces. The monospace terminal feel. These aren't arbitrary choices — they're Courier's personality. This token system doesn't replace that identity; it gives it **precision**.

**What stays the same:**
- Cyan/teal as the primary action color — fast, digital, precise.
- Amber/orange as the secondary attention color — urgent but not alarming.
- Cool slate grays — professional, cold-tech, infrastructure-grade.
- Dark-first — this is a tool for engineers, not a marketing site.

**What gets sharper:**
- Every gray now has a consistent cool undertone (hue 250) instead of ad-hoc neutrals.
- Alpha-based borders and text adapt to any surface instead of hardcoded values that break on different backgrounds.
- Signal colors follow infrastructure conventions (success/warning/error/info), not vague labels.
- Chart colors are perceptually balanced in OKLCH instead of "whatever looks okay in hex."
- Capacity tokens encode thresholds (safe/warn/critical) as first-class design primitives.

**What this enables:**
- A developer can build any Courier screen from this document without asking "what color should this be?"
- Every surface, border, and text value has a clear reason for existing.
- The system scales — adding new components doesn't require inventing new colors.
- Light mode (if ever needed) can be built by remapping these same semantic tokens to inverted values.

> Courier doesn't look like a design system demo. It looks like infrastructure.
> The tokens should be invisible — when the dashboard feels right, nobody notices the color system. They just trust the tool.

---

## Quick Reference: Token Cheat Sheet

| Need | Token | Tailwind Class |
|------|-------|----------------|
| Page background | `--bg` | `bg-gray-3` |
| Card background | `--bg-elevated` | `bg-gray-4` |
| Primary text | `--t1` | `text-t1` |
| Secondary text | `--t2` | `text-t2` |
| Tertiary text | `--t3` | `text-t3` |
| Primary action | `--brand` | `bg-brand` / `text-brand` |
| Action hover | `--brand-hover` | `bg-brand-hover` |
| Subtle highlight | `--brand-10` | `bg-brand-10` |
| Card border | `--border-default` | `border-border` |
| Success state | `--success` | `text-success` |
| Success bg | `--success-10` | `bg-success-10` |
| Warning state | `--warning` | `text-warning` |
| Error state | `--error` | `text-error` |
| Code block bg | `--code-bg` | `bg-gray-2` |
| Card radius | `--radius-card` | `rounded-card` |
| Card padding | `--card-padding` | `p-5` (20px) |
| Focus ring | `--shadow-focus` | `shadow-focus` |
