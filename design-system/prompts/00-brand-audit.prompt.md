# Step 0: Brand Audit — Extract Every Visual Value

## Purpose

Scan Courier's existing codebase and extract every visual value currently in use. Catalog colors, fonts, spacing, borders, shadows, transitions, and z-indexes. Flag inconsistencies and orphaned values. This audit becomes the source of truth for all subsequent design system work.

## Dependencies

None — this is the first step.

## Quality Context

Before doing ANY work, read these files for quality standards and engineering principles:

```
skills/ui-engineering/SKILL.md
constitution/ENGINEERING-CONSTITUTION.md
```

These define the quality floor. Every decision in this audit should reflect those standards.

## MCP Check

If a Courier MCP server is connected (check `.mcp.json` or `claude mcp list`), query it for any UI-relevant stored knowledge before scanning files. Use `search_knowledge` with queries like "design tokens", "color palette", "typography", "UI components" to surface any existing documentation or decisions.

## Files to Scan

Scan these locations in the Courier codebase. Read every file — do not skip any.

### Core Style Files
- `src/app/globals.css` (or `src/styles/globals.css` — find whichever exists)
- `tailwind.config.ts` (may not exist if using Tailwind v4 CSS-first config)
- `postcss.config.mjs` or `postcss.config.js`
- `src/app/layout.tsx` and any nested `layout.tsx` files (`src/app/**/layout.tsx`)

### Component Files
- All files under `src/components/**/*` — every `.tsx`, `.ts`, `.css`, `.module.css` file
- Look for inline styles, Tailwind classes, CSS modules, and styled-components patterns

### Page Files
- All files under `src/app/**/page.tsx`
- Check for page-level style overrides

### Config Files
- `next.config.ts` or `next.config.mjs` (font loading config)
- `components.json` (shadcn config — contains theme/style preferences)

## What to Extract

For each category below, create a comprehensive table in the output document.

### 1. Colors

Extract EVERY color value from every source:

| Source Type | What to Look For |
|------------|-----------------|
| CSS custom properties | `--color-*`, `--background`, `--foreground`, any `--` prefixed color |
| Tailwind classes | `bg-*`, `text-*`, `border-*`, `ring-*`, `shadow-*` with color values |
| Raw hex values | `#xxx`, `#xxxxxx`, `#xxxxxxxx` anywhere in code |
| HSL values | `hsl(...)`, `hsla(...)` |
| OKLCH values | `oklch(...)` |
| RGB values | `rgb(...)`, `rgba(...)` |

Group every found color by its ROLE:
- **Background**: page bg, card bg, surface bg, input bg, hover bg
- **Text**: primary text, secondary text, muted text, placeholder text
- **Border**: card borders, input borders, divider lines, focus rings
- **Accent/Brand**: primary actions, links, active states, highlights
- **Status**: success, warning, error, info indicators
- **Other**: shadows, overlays, gradients

### 2. Typography

| Property | What to Extract |
|----------|----------------|
| font-family | Every `font-family` declaration, `--font-*` variable, Tailwind `font-*` class |
| font-size | Every `font-size` value, `text-*` class, `--text-*` variable |
| font-weight | Every `font-weight` value, `font-*` weight class |
| line-height | Every `line-height` value, `leading-*` class |
| letter-spacing | Every `letter-spacing` value, `tracking-*` class |
| Font loading | `@font-face` declarations, `next/font` imports, WOFF2 file references |

Note which fonts are actually loaded vs. referenced but potentially missing.

### 3. Spacing

| Property | What to Extract |
|----------|----------------|
| padding | Every `p-*`, `px-*`, `py-*`, `pt-*`, `pr-*`, `pb-*`, `pl-*` class + raw values |
| margin | Every `m-*`, `mx-*`, `my-*`, `mt-*`, `mr-*`, `mb-*`, `ml-*` class + raw values |
| gap | Every `gap-*`, `gap-x-*`, `gap-y-*` class + raw values |
| width/height | Fixed dimensions, `w-*`, `h-*`, `min-*`, `max-*` |

### 4. Borders

| Property | What to Extract |
|----------|----------------|
| border-radius | Every `rounded-*` class, `border-radius` value, `--radius` variable |
| border-width | Every `border-*` width class, raw border-width values |
| border-color | Every border color (may overlap with Colors section — cross-reference) |
| outline/ring | `ring-*`, `outline-*` classes and values |

### 5. Shadows

| Property | What to Extract |
|----------|----------------|
| box-shadow | Every `shadow-*` class, raw `box-shadow` value, `--shadow-*` variable |
| drop-shadow | Any `drop-shadow-*` usage |

Note: Courier's design identity is border-based hierarchy on dark backgrounds, NOT shadow-based. Flag any heavy shadow usage as a potential inconsistency.

### 6. Transitions & Animations

| Property | What to Extract |
|----------|----------------|
| transition | Every `transition-*` class, raw `transition` values, durations, easing functions |
| animation | Every `animate-*` class, `@keyframes` declarations |
| duration | Every timing value in use |
| easing | Every easing function (`ease-*`, cubic-bezier, etc.) |

### 7. Z-Index

Extract every `z-*` class and raw `z-index` value. Map each to its purpose (nav, sidebar, modal, dropdown, toast, etc.).

## Inconsistency Analysis

After extraction, analyze and flag:

1. **Same role, different values**: e.g., card backgrounds using 3 different colors across components
2. **Raw values that should be tokens**: any hardcoded `#hex`, `hsl()`, pixel value that appears 2+ times
3. **Orphaned tokens**: CSS variables defined but never used
4. **Missing tokens**: Repeated raw values that have no corresponding CSS variable
5. **Tailwind class inconsistencies**: same visual intent achieved with different utility combinations
6. **Scale gaps**: spacing or sizing that doesn't follow a consistent scale
7. **Dark mode gaps**: values defined for one mode but not the other

## Output

Write the complete audit to:

```
src/design-system/BRAND-AUDIT.md
```

Structure the document as:

```markdown
# Courier Brand Audit

> Generated by design system pipeline, Step 0
> Date: [current date]
> Source: Full codebase scan

## Summary
[2-3 sentences: total tokens found, major patterns, top inconsistencies]

## Colors
### CSS Variables
[table: variable name | value | where used | role]

### Tailwind Classes in Use
[table: class | count of usages | role]

### Raw Values
[table: value | where used | suggested token name]

## Typography
[same table pattern]

## Spacing
[same table pattern]

## Borders
[same table pattern]

## Shadows
[same table pattern]

## Transitions & Animations
[same table pattern]

## Z-Index Map
[table: value | purpose | where used]

## Inconsistencies
### Critical (blocks design system)
[numbered list with file:line references]

### Warning (should fix)
[numbered list with file:line references]

### Info (minor cleanup)
[numbered list with file:line references]

## Recommendations
[Bulleted list of specific actions for Steps 1-3]
```

## Definition of Done

- [ ] Every CSS variable in globals.css is cataloged with its value and role
- [ ] Every unique Tailwind color/spacing/typography class used across components is listed
- [ ] Every raw (non-tokenized) value that appears 2+ times is flagged
- [ ] Font loading is verified — confirm GeneralSans and HubotSans WOFF2 files exist and load
- [ ] Inconsistencies are categorized by severity
- [ ] The BRAND-AUDIT.md file exists at `src/design-system/BRAND-AUDIT.md`
- [ ] The audit is specific enough that someone could rebuild the entire visual system from it alone
