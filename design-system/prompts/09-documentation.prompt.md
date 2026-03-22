# Step 9: Documentation — Living Design System Docs

## Purpose

Build comprehensive, practical documentation for Courier's design system. When complete, a developer who has never seen the codebase can understand the system in 5 minutes, find any token in 10 seconds, and build a new page in 30 minutes using the docs alone. The design system becomes self-documenting.

## Dependencies

Steps 0-8 must be complete:
- Step 0: Brand audit (token extraction)
- Step 1: Token foundation (globals.css)
- Step 2: Core primitives
- Step 3: Typography system
- Step 4: Core components
- Step 5: Data components
- Step 6: Navigation components
- Step 7: Motion & interaction system
- Step 8: Page patterns (reference implementations)

Everything is built. This step documents it all and performs a final quality pass.

## Quality Context

Before doing ANY work, read these files:

```
skills/ui-engineering/SKILL.md
constitution/ENGINEERING-CONSTITUTION.md
```

Documentation quality reflects engineering quality. These docs are a product — treat them with the same rigor as code.

## Instructions

### Phase 1: Full Codebase Read

Read every file that will be documented:

1. `src/app/globals.css` — every token
2. Every component file under `src/components/` and `src/design-system/`
3. Every page file built in Step 8
4. Every existing doc file under `src/design-system/docs/`
5. `src/lib/mock-data.ts`
6. Layout files (`src/app/**/layout.tsx`)

You must have complete awareness of the system before documenting it. Do not skip any file.

### Phase 2: Master Index

Create `src/design-system/INDEX.md`:

**Structure:**

```markdown
# Courier Design System

> Local AI model serving platform for Apple Silicon.
> Dark-first, cyan/teal primary, amber/orange secondary.
> GeneralSans body, HubotSans Black headings.

## Quick Start

1. **Read this file** — you're here, good start
2. **Browse tokens** — `docs/tokens-reference.md` has every CSS variable
3. **Find a component** — file map below, or `docs/quick-reference.md` for the decision tree

## Stack

- Next.js 16.1.1, React 19
- Tailwind CSS v4 (OKLCH color space, CSS-first config)
- shadcn/ui (customized with Courier tokens)
- Lucide React icons
- GeneralSans (body) + HubotSans Black (headings)

## File Map

### Tokens & Styles
| File | Purpose |
|------|---------|
| `src/app/globals.css` | All design tokens (color, spacing, typography, motion, z-index) |

### Core Primitives (Step 2)
| File | Component | Description |
|------|-----------|-------------|
[List every component file with its export name and one-line description]

### Core Components (Step 4)
[Same table format]

### Data Components (Step 5)
[Same table format]

### Navigation Components (Step 6)
[Same table format]

### Page Patterns (Step 8)
| File | Page | Key Components Used |
|------|------|---------------------|
[List every reference page]

### Documentation
| File | Contents |
|------|----------|
[List every doc file]

## Dependency Graph

Steps build on each other:

Step 0 (Audit) → Step 1 (Tokens) → Step 2 (Primitives) → Step 3 (Typography)
                                  ↘ Step 4 (Core) → Step 5 (Data) → Step 6 (Nav)
                                                                    ↘ Step 7 (Motion)
                                                                      ↘ Step 8 (Pages)
                                                                        ↘ Step 9 (Docs)

Tokens are foundational — every component depends on globals.css.
Motion (Step 7) was applied retroactively to all components.
Pages (Step 8) compose components — they create no new primitives.
```

Fill in every table row with actual file paths and component names from the codebase. Do not use placeholders. Read the files and document what actually exists.

### Phase 3: Token Reference

Create `src/design-system/docs/tokens-reference.md`:

**Structure:**

```markdown
# Token Reference

Complete reference for every CSS variable in Courier's design system.
Source: `src/app/globals.css`

## Color Tokens

### Semantic Colors
| Token | Dark Value | Light Value | Usage |
|-------|-----------|-------------|-------|
| `--background` | ... | ... | Page background |
| `--foreground` | ... | ... | Primary text |
[Every semantic color token]

### Brand Colors
| Token | Value | Usage |
|-------|-------|-------|
[Primary cyan/teal, secondary amber/orange, etc.]

### Component Colors
[Sidebar, card, muted, accent, destructive, etc.]

## Typography Tokens

| Token | Value | Usage |
|-------|-------|-------|
| `--font-body` | GeneralSans | Body text, UI labels |
| `--font-heading` | HubotSans Black | Headings, display text |
[Font sizes, line heights, letter spacing]

### Type Scale Classes
| Class | Size | Weight | Line Height | Usage |
|-------|------|--------|-------------|-------|
[Every type class defined in the system]

## Spacing

| Token | Value | Tailwind | Usage |
|-------|-------|----------|-------|
[Every spacing token, with Tailwind class equivalent]

## Motion Tokens

| Token | Value | Usage |
|-------|-------|-------|
| `--duration-instant` | 100ms | Opacity toggles, icon swaps |
| `--duration-fast` | 150ms | Hover states, button feedback |
| `--duration-normal` | 200ms | Card transitions, focus rings |
| `--duration-smooth` | 300ms | Modals, sheets, page transitions |
| `--duration-exit` | 210ms | Exit animations |
| `--ease-out` | cubic-bezier(0.16, 1, 0.3, 1) | Entering elements |
| `--ease-in` | cubic-bezier(0.4, 0, 1, 1) | Exiting elements |
| `--ease-in-out` | cubic-bezier(0.4, 0, 0.2, 1) | Looping animations |

## Border & Radius

| Token | Value | Usage |
|-------|-------|-------|
[Every radius, border-width, border-color token]

## Shadows

| Token | Value | Usage |
|-------|-------|-------|
[Every shadow token]

## Z-Index

| Token | Value | Usage |
|-------|-------|-------|
[Every z-index token, ordered by value]
```

Read `globals.css` and extract every actual value. Do not guess. Document what exists.

### Phase 4: Quick Reference

Create `src/design-system/docs/quick-reference.md`:

**Structure:**

```markdown
# Quick Reference

One-page cheat sheet for building with Courier's design system.

## Token Quick-Lookup

### Colors (most used)
| Need | Token | Tailwind |
|------|-------|----------|
| Page background | `--background` | `bg-background` |
| Primary text | `--foreground` | `text-foreground` |
| Muted text | `--muted-foreground` | `text-muted-foreground` |
| Brand primary | `--primary` | `bg-primary` |
| Brand secondary | `--secondary` | `bg-secondary` |
| Danger/error | `--destructive` | `bg-destructive` |
[Top 15 most-used color tokens]

### Spacing (most used)
| Need | Value | Tailwind |
|------|-------|----------|
| Tight gap | 4px | `gap-1` |
| Standard gap | 8px | `gap-2` |
| Section gap | 16px | `gap-4` |
| Page padding | 24px | `p-6` |
[Common spacing patterns]

### Typography (most used)
| Need | Class/Token |
|------|-------------|
| Page title | [class name] |
| Section header | [class name] |
| Body text | [class name] |
| Small/caption | [class name] |
| Code/mono | [class name] |

## Component Import Paths

| Component | Import |
|-----------|--------|
| Button | `@/components/ui/button` |
| Badge | `@/components/ui/badge` |
[Every component with its import path]

## Common Patterns

### Add a new page
1. Create route file in `src/app/(dashboard)/your-page/page.tsx`
2. Import components from `@/components/`
3. Use mock data from `@/lib/mock-data.ts` or create real data fetcher
4. Follow the reference pages in Step 8 for layout patterns
5. Add loading skeleton (compose `.skeleton` classes)
6. Add empty state using `EmptyState` component

### Create a metric card row
```tsx
<div className="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-4 gap-4">
  <MetricCard title="..." value="..." subtitle="..." />
  {/* repeat */}
</div>
```

### Add a chart with controls
```tsx
<div className="space-y-4">
  <TimeRangeToggle value={range} onChange={setRange} />
  <ChartContainer>
    <AreaChart data={filteredData} />
  </ChartContainer>
</div>
```

### Add a filtered list
```tsx
<TabBar tabs={["All", "Active", "Stopped"]} value={tab} onChange={setTab} />
<div className="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-4">
  {filteredItems.map(item => <Card key={item.id} {...item} />)}
</div>
{filteredItems.length === 0 && <EmptyState icon={...} title="..." description="..." />}
```

## Which Component Do I Use?

| I need to... | Use |
|--------------|-----|
| Show a single number with context | MetricCard |
| Display a data series over time | ChartContainer + AreaChart/LineChart |
| Show a sortable list of records | DataTable |
| Display a model's status and info | ModelCard |
| Let users pick between views | TabBar |
| Let users pick a time range | TimeRangeToggle |
| Show an action button | Button |
| Show a status label | Badge |
| Collect text input | Input |
| Show nothing-here state | EmptyState |
| Show loading placeholder | .skeleton / .skeleton-circle / .skeleton-pill classes |
| Show a modal or confirmation | Dialog |
| Navigate between sections | Sidebar (app shell) |
| Group settings or form fields | Separator between sections |
```

Adapt component names to match what actually exists in the codebase.

### Phase 5: Per-Component Documentation Consolidation

Review every doc file created in Steps 4-6 (under `src/design-system/docs/`). For each:

1. Verify it follows this consistent format:
   - **Description** — one paragraph, what the component is and when to use it
   - **Import** — exact import path
   - **Props** — table with Name, Type, Default, Description columns
   - **Variants** — list of variants with visual description
   - **Usage Example** — complete, copy-pasteable TSX snippet
   - **Do / Don't** — at least 2 of each, specific to this component
   - **Accessibility** — keyboard behavior, ARIA attributes, screen reader notes

2. If any section is missing, add it by reading the component source.

3. If the format is inconsistent (different heading levels, missing props, etc.), standardize it.

### Phase 6: Contributing Guide

Create `src/design-system/docs/contributing.md`:

```markdown
# Contributing to Courier's Design System

## How to Add a New Token

1. Open `src/app/globals.css`
2. Find the appropriate section (Color, Spacing, Motion, etc.)
3. Add the token with a comment explaining its purpose
4. If the token has theme variants, add it in BOTH `:root` (dark) and `.light` (or `:root.light`) blocks
5. Add the corresponding Tailwind utility if needed (Tailwind v4 auto-generates from CSS variables)
6. Document it in `docs/tokens-reference.md`

## How to Add a New Component

1. Create the component file in `src/components/` (or `src/components/ui/` for primitives)
2. Use only existing tokens — never hardcode colors, spacing, or timing values
3. Add TypeScript types for all props (use `interface`, not `type` for component props)
4. Add JSDoc on the default export with a one-line description
5. Ensure all interactive states are handled (hover, active, focus-visible, disabled)
6. Wrap hover styles in `@media (hover: hover)`
7. Test at xl / md / sm breakpoints
8. Add a doc file in `src/design-system/docs/` following the standard format
9. Add the component to `INDEX.md` file map

## How to Add a New Page Pattern

1. Create the route file in `src/app/(dashboard)/your-page/page.tsx`
2. Import only from the existing component library — do not create new primitives
3. Add mock data to `src/lib/mock-data.ts`
4. Create a loading skeleton state
5. Handle empty/zero-data state with `EmptyState` component
6. Verify responsive behavior at 3 breakpoints (xl, md, sm)
7. Add to the page inventory in `docs/page-patterns.md`

## Quality Checklist

Before submitting a PR that touches the design system:

- [ ] No raw color values (use semantic tokens)
- [ ] No raw timing values (use duration tokens)
- [ ] No raw easing values (use easing tokens)
- [ ] No bare `:hover` rules (wrap in `@media (hover: hover)`)
- [ ] `:focus-visible` uses double-ring box-shadow, not outline
- [ ] Disabled state: opacity 0.5, cursor not-allowed, no transform
- [ ] Responsive at xl / md / sm breakpoints
- [ ] Loading skeleton exists for async content
- [ ] Empty state handled for zero-data conditions
- [ ] JSDoc on exported component
- [ ] Props have TypeScript types
- [ ] Doc file exists or updated in `src/design-system/docs/`

## Naming Conventions

| Thing | Convention | Example |
|-------|-----------|---------|
| Component files | kebab-case | `model-card.tsx` |
| Component names | PascalCase | `ModelCard` |
| CSS tokens | kebab-case with `--` prefix | `--duration-fast` |
| Tailwind utilities | kebab-case | `text-foreground` |
| Doc files | kebab-case | `model-card.md` |
| Page routes | kebab-case | `/workbench` |
| Mock data exports | camelCase | `mockModels` |
| Types/Interfaces | PascalCase | `ModelCardProps` |
```

### Phase 7: Changelog

Create `src/design-system/CHANGELOG.md`:

```markdown
# Changelog

All notable changes to Courier's design system are documented here.

## [0.1.0] — 2026-03-21

### Initial Release — Complete Design System

Built from scratch across 9 sequential steps:

#### Step 0: Brand Audit
- Extracted all existing visual values from the Courier codebase
- Cataloged colors, fonts, spacing, borders, shadows, transitions, z-indexes
- Identified inconsistencies and orphaned values

#### Step 1: Token Foundation
- Established `globals.css` as the single source of truth for all design tokens
- Defined OKLCH color palette (dark-first, cyan/teal primary, amber/orange secondary)
- Set up typography scale (GeneralSans body, HubotSans Black headings)
- Defined spacing, radius, shadow, and z-index systems

#### Step 2: Core Primitives
- Button, Badge, Input, and other foundational UI elements
- All variants, sizes, and states defined

#### Step 3: Typography System
- Type scale classes with responsive sizing
- Font loading and fallback strategy

#### Step 4: Core Components
- Cards, dialogs, and other structural components
- All built on token system from Step 1

#### Step 5: Data Components
- Charts (Area, Line, Bar) with ChartContainer
- DataTable with sorting
- MetricCard for KPI display
- ModelCard for model status display
- TimeRangeToggle for chart filtering

#### Step 6: Navigation Components
- Sidebar with collapsible behavior
- TabBar for view switching
- App Shell layout composing sidebar + content area

#### Step 7: Motion & Interaction
- Duration tokens (instant/fast/normal/smooth/exit)
- Easing tokens (ease-out/ease-in/ease-in-out)
- Hover guards, active press, focus rings, disabled states
- Touch safety, skeleton loading, theme transition blocker
- Reduced motion support
- Audited and standardized all existing transitions

#### Step 8: Page Patterns
- Dashboard (metrics, charts, model grid, activity feed)
- Model Workbench (tabs, search, filtered grid, empty states)
- Analytics (time range, multi-chart, data table)
- Settings (API keys, toggles, sections)
- EmptyState reusable component
- All pages responsive, with loading skeletons and mock data

#### Step 9: Documentation
- INDEX.md master index with file map and dependency graph
- Token reference (every CSS variable, organized by category)
- Quick reference (cheat sheet, decision tree, common patterns)
- Per-component docs (props, variants, usage, do/don't)
- Contributing guide (how to extend the system)
- This changelog

---

## Template for Future Entries

## [X.X.X] — YYYY-MM-DD

### Added
- New features or components

### Changed
- Changes to existing components or tokens

### Fixed
- Bug fixes

### Removed
- Removed features, components, or deprecated tokens
```

Adjust dates and specific details to match what was actually built. Read the files and document reality, not assumptions.

### Phase 8: Final Quality Pass

After all documentation is written, perform a final sweep:

1. **JSDoc audit** — Read every component file's default export. If it lacks a JSDoc comment, add one:
   ```typescript
   /** Displays a model's status, resource usage, and key metrics. */
   export function ModelCard({ ... }: ModelCardProps) {
   ```
   One line. What it does, not how.

2. **CSS comment audit** — Read `globals.css` from top to bottom. Every section must have a header comment:
   ```css
   /* ============ Color Tokens ============ */
   /* ============ Typography ============ */
   /* ============ Spacing ============ */
   /* ============ Motion ============ */
   /* ============ Z-Index ============ */
   /* ============ Skeleton Loading ============ */
   ```
   If any section lacks a header, add one.

3. **Orphaned token check** — For every CSS variable defined in `globals.css`, search the component files for usage. If a token is defined but never referenced anywhere (not in components, not in Tailwind classes, not in docs), flag it in a comment:
   ```css
   --orphaned-token: value; /* UNUSED — consider removing */
   ```

4. **Raw value check** — Search all component files for:
   - Hardcoded color values (`#`, `rgb(`, `hsl(`, `oklch(` not inside globals.css)
   - Hardcoded durations (`150ms`, `200ms`, `0.3s` not inside globals.css)
   - Hardcoded easings (`ease`, `ease-in-out`, `cubic-bezier` not inside globals.css)
   - `transition: all` (should specify properties)

   Fix any violations found. Every value must come from a token.

## Output Checklist

When complete, verify:

- [ ] `src/design-system/INDEX.md` exists with complete file map and dependency graph
- [ ] `src/design-system/docs/tokens-reference.md` has every CSS variable from globals.css
- [ ] `src/design-system/docs/quick-reference.md` has token lookup tables and decision tree
- [ ] All per-component docs follow consistent format (description, import, props, variants, usage, do/don't, accessibility)
- [ ] `src/design-system/docs/contributing.md` has clear instructions for extending the system
- [ ] `src/design-system/CHANGELOG.md` documents the full build history
- [ ] Every component's default export has a JSDoc comment
- [ ] Every section in globals.css has a header comment
- [ ] No orphaned tokens (or they are flagged)
- [ ] No raw values in component files (everything uses tokens)
- [ ] Documentation is accurate — it reflects what actually exists, not what was planned

## Definition of Done

A new developer can read `INDEX.md`, understand the entire design system in 5 minutes, find any token in 10 seconds using the token reference, and build a new page in 30 minutes using the quick reference and page patterns docs. Every component is documented with props, usage examples, and do/don't guidance. The contributing guide makes it clear how to extend the system without breaking conventions. The design system is self-documenting — the docs are the product.
