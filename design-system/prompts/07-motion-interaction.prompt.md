# Step 7: Motion & Interaction — System-Wide Behavior

## Purpose

Audit and standardize all motion and interaction patterns across the entire Courier component library. This is a refinement pass — Steps 0-6 built the components; this step ensures every transition, hover state, focus ring, and loading pattern is consistent, accessible, and uses the motion token system.

## Dependencies

Steps 0-6 must be complete:
- Step 0: Brand audit (tokens extracted)
- Step 1: Token foundation (globals.css structured)
- Step 2: Core primitives (Button, Badge, Input, etc.)
- Step 3: Typography system
- Step 4: Core components
- Step 5: Data components (charts, tables, metrics)
- Step 6: Navigation components (sidebar, tabs, app shell)

All component files exist. This step touches every one of them.

## Quality Context

Before doing ANY work, read these files for quality standards and engineering principles:

```
skills/ui-engineering/SKILL.md
constitution/ENGINEERING-CONSTITUTION.md
skills/ui-engineering/references/quality-checklist.md (interaction section specifically)
```

These define the quality floor. Every motion decision must reflect those standards. Athlete Era interaction patterns are the benchmark — study them if referenced in the quality checklist.

## Instructions

### Phase 1: Full Codebase Read

Read every file that will be touched:

1. `src/app/globals.css` — the token source of truth
2. Every component file under `src/components/` and `src/design-system/`
3. Any layout files (`src/app/**/layout.tsx`)
4. `skills/ui-engineering/references/quality-checklist.md` — interaction section

Do not skip any file. You need complete awareness of what exists before auditing.

### Phase 2: Audit Existing Transitions

Search the entire codebase for every instance of:
- `transition` (CSS property)
- `animation` (CSS property)
- `@keyframes`
- `transform` on interactive elements
- `duration-` (Tailwind classes)
- `ease-` (Tailwind classes)
- `animate-` (Tailwind classes)

For each instance found, verify:

1. **Duration uses tokens, not arbitrary values.** Replace `150ms`, `200ms`, `0.3s`, `300ms` etc. with the appropriate token:
   - `var(--duration-instant)` for opacity toggles, icon swaps
   - `var(--duration-fast)` for hover states, button feedback
   - `var(--duration-normal)` for card transitions, focus rings
   - `var(--duration-smooth)` for modals, sheets, page transitions
   - `var(--duration-exit)` for any exit/close/dismiss animation

2. **Easing uses tokens, not CSS keyword easings.** Replace `ease`, `ease-in`, `ease-out`, `ease-in-out`, `linear` with:
   - `var(--ease-out)` for elements entering the viewport
   - `var(--ease-in)` for elements exiting the viewport
   - `var(--ease-in-out)` for looping/continuous animations

3. **No animation on high-frequency interactions.** These must be instant (no transition):
   - Tab switches (active tab highlight)
   - Status indicator updates (online/offline dots)
   - List item additions/removals during data refresh
   - Counter/number updates
   - Checkbox/radio state changes

### Phase 3: Motion Token System in globals.css

Verify these tokens exist in `globals.css`. If any are missing, add them in a clearly commented `/* ============ Motion ============ */` section:

```css
/* ============ Motion ============ */

/* Durations — use these, never raw ms/s values */
--duration-instant: 100ms;   /* opacity toggles, icon swaps */
--duration-fast: 150ms;      /* hover states, button feedback */
--duration-normal: 200ms;    /* card transitions, focus rings */
--duration-smooth: 300ms;    /* modals, sheets, page transitions */
--duration-exit: 210ms;      /* slightly faster than enter — feels snappier */

/* Easing — use these, never CSS keyword easings */
--ease-out: cubic-bezier(0.16, 1, 0.3, 1);    /* entering elements */
--ease-in: cubic-bezier(0.4, 0, 1, 1);          /* exiting elements */
--ease-in-out: cubic-bezier(0.4, 0, 0.2, 1);    /* looping / continuous */
```

If tokens exist but with different values, update them to match these exact values. These are the canonical motion values for Courier.

### Phase 4: Interaction Pattern Verification

Go through every component file and verify/implement each of these patterns:

#### a. Hover Guards

Every `:hover` rule must be wrapped in `@media (hover: hover)`:

```css
@media (hover: hover) {
  .component:hover {
    /* hover styles */
  }
}
```

This prevents hover styles from sticking on touch devices. Search for any bare `:hover` pseudo-class and wrap it. In Tailwind, use `@media (hover: hover)` in globals.css for custom hover utilities, or ensure component-level hover styles are guarded.

#### b. Active Press Feedback

Every clickable element must have active press feedback:

```css
button:active:not([disabled]),
[role="button"]:active:not([disabled]),
a:active:not([disabled]) {
  transform: scale(0.97);
}
```

Add this as a global rule in `globals.css` if not already present. Verify it doesn't conflict with component-level transforms.

#### c. Focus Rings

Every focusable element must use the double-ring box-shadow pattern on `:focus-visible`:

```css
:focus-visible {
  outline: none;
  box-shadow: 0 0 0 2px var(--background), 0 0 0 4px var(--primary);
}
```

The inner ring matches the background (creating a gap), the outer ring uses the brand color. Never use `outline` for focus indicators — always `box-shadow` for the double-ring effect.

Search for any `outline:` on focus states and replace with the box-shadow pattern.

#### d. Disabled States

Every component with a disabled state must have:

```css
[disabled],
[aria-disabled="true"] {
  opacity: 0.5;
  cursor: not-allowed;
  pointer-events: none;
  transform: none !important;
}
```

Verify this exists as a global rule. Check that individual components don't override this with inconsistent disabled styling.

#### e. Touch Safety

Add these global rules if not present:

```css
/* Prevent tap highlight on iOS/Android */
button, a, [role="button"], input, select, textarea, [tabindex] {
  -webkit-tap-highlight-color: transparent;
}

/* Prevent double-tap zoom on interactive elements */
button, a, [role="button"] {
  touch-action: manipulation;
}

/* Prevent iOS zoom on input focus — inputs must be >= 16px */
input, select, textarea {
  font-size: max(16px, 1em);
}
```

#### f. Skeleton Loading System

Verify or create the skeleton loading system in `globals.css`:

```css
/* ============ Skeleton Loading ============ */

--skeleton-duration: 1.5s;

@keyframes shimmer {
  0% { background-position: -200% 0; }
  100% { background-position: 200% 0; }
}

.skeleton {
  background: linear-gradient(
    90deg,
    var(--muted) 25%,
    var(--muted-foreground / 0.08) 50%,
    var(--muted) 75%
  );
  background-size: 200% 100%;
  animation: shimmer var(--skeleton-duration) ease-in-out infinite;
  border-radius: var(--radius);
}

.skeleton-circle {
  composes: skeleton; /* or extend via @apply if Tailwind */
  border-radius: 9999px;
}

.skeleton-pill {
  composes: skeleton;
  border-radius: 9999px;
  height: 1.5em;
  width: 6em;
}
```

If using Tailwind v4's CSS-first approach, implement as utility classes or in the base layer.

#### g. Theme Transition Blocker

Add the theme switching guard to prevent flash of wrong-themed content:

```css
/* Applied via JS during theme swap to prevent transition flash */
.theme-switching,
.theme-switching *,
.theme-switching *::before,
.theme-switching *::after {
  transition-duration: 0s !important;
  animation-duration: 0s !important;
}
```

Document the JS usage pattern in a comment:

```css
/*
  Usage in theme toggle:
  document.documentElement.classList.add('theme-switching');
  // swap theme
  document.documentElement.offsetHeight; // force reflow
  document.documentElement.classList.remove('theme-switching');
*/
```

#### h. Reduced Motion

Add the global reduced motion override:

```css
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

This is non-negotiable. Accessibility is not optional.

### Phase 5: Anti-Patterns Documentation

Create `src/design-system/docs/motion-interaction.md` with the following structure:

1. **Overview** — what the motion system is, why it exists
2. **Token Reference** — table of all duration and easing tokens with use cases
3. **Interaction Patterns** — each pattern (hover, active, focus, disabled, touch, skeleton, theme, reduced-motion) with code examples
4. **Anti-Patterns** — what NOT to do:
   - Do NOT animate list items appearing/disappearing individually (stagger effects). Data lists update atomically.
   - Do NOT use spring physics for infrastructure UIs. Courier serves ML models — the UI should feel precise, not playful.
   - Do NOT animate charts redrawing on filter change. Chart data swaps instantly; only initial render can animate.
   - Do NOT add entrance animations to data that updates frequently (metrics, status indicators, logs).
   - Do NOT use `ease-in` for entering elements — it starts slow and feels sluggish. Use `ease-out` (fast start, gentle stop).
   - Do NOT use `transition: all` — always specify exact properties (`transition: opacity, transform`).
   - Do NOT animate `width`, `height`, `top`, `left` — these trigger layout. Use `transform` and `opacity` only.
5. **Decision Flowchart** — "Should I animate this?" (text-based):
   - Is it a data update? → No animation
   - Is it user-initiated? → Yes, use appropriate duration
   - Is it entering the viewport? → ease-out + duration-smooth
   - Is it a hover/focus response? → ease-out + duration-fast
   - Is it exiting? → ease-in + duration-exit
   - Is it looping? → ease-in-out + custom duration

## Output Checklist

When complete, verify:

- [ ] `globals.css` has complete motion token section with all durations and easings
- [ ] `globals.css` has skeleton loading keyframes and classes
- [ ] `globals.css` has theme transition blocker
- [ ] `globals.css` has reduced motion media query
- [ ] `globals.css` has global touch safety rules
- [ ] `globals.css` has global active press feedback
- [ ] `globals.css` has global focus ring pattern
- [ ] `globals.css` has global disabled state rules
- [ ] Every component file uses duration tokens (no raw `150ms`, `200ms`, `0.3s`)
- [ ] Every component file uses easing tokens (no `ease`, `ease-in-out` keywords)
- [ ] Every `:hover` rule is wrapped in `@media (hover: hover)`
- [ ] No animation on high-frequency interactions (tabs, status, counters)
- [ ] `src/design-system/docs/motion-interaction.md` exists with full documentation
- [ ] Anti-patterns section is thorough and specific to Courier's domain

## Definition of Done

Every component uses motion tokens consistently. No arbitrary transition values anywhere in the codebase. Hover guards on every hover rule. Reduced motion respected globally. Theme switching is flash-free. The motion system is documented with clear do/don't guidance. A developer who has never seen the codebase can read the docs and make correct motion decisions on their first try.
