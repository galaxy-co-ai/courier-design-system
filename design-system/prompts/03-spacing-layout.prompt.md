# Step 3: Spacing & Layout — Grid System & App Shell

## Purpose

Define Courier's spacing scale, semantic layout tokens, z-index system, breakpoints, and build the foundational App Shell layout component. This is the structural skeleton — every page, panel, and component aligns to this grid. After this step, the design system's foundational layer (color + typography + spacing + layout) is complete.

## Dependencies

**Required before starting:**
- `src/design-system/BRAND-AUDIT.md` — spacing/layout findings from Step 0
- `src/app/globals.css` (or `src/styles/globals.css`) — must contain the color system (Step 1) AND typography system (Step 2). This step adds spacing/layout tokens to the same file. Do NOT overwrite previous sections.

## Quality Context

Read these before doing any work:

```
skills/ui-engineering/SKILL.md
skills/ui-engineering/references/design-tokens.md   ← Spacing scale + layout token architecture
constitution/ENGINEERING-CONSTITUTION.md
```

## Pre-Work: Audit Review

Read `src/design-system/BRAND-AUDIT.md` and note:
1. What spacing values are currently in use? Are they on a consistent grid?
2. What layout dimensions exist? (sidebar widths, nav heights, max-widths)
3. What z-index values are in use? Are there collisions?
4. Are there existing layout components? (`app-shell`, `sidebar`, `main-layout`, etc.)

Also scan:
- `src/components/layout/` or `src/components/` for any existing layout components
- `src/app/layout.tsx` for current page structure
- Check if shadcn's Sidebar component is installed (`src/components/ui/sidebar.tsx`)

## Implementation

### A. Base Spacing Scale (4px Grid)

Every spacing value is a multiple of 4px. No exceptions.

```css
@theme inline {
  --spacing-0: 0px;
  --spacing-px: 1px;
  --spacing-0-5: 2px;
  --spacing-1: 4px;
  --spacing-1-5: 6px;
  --spacing-2: 8px;
  --spacing-2-5: 10px;
  --spacing-3: 12px;
  --spacing-3-5: 14px;
  --spacing-4: 16px;
  --spacing-5: 20px;
  --spacing-6: 24px;
  --spacing-7: 28px;
  --spacing-8: 32px;
  --spacing-9: 36px;
  --spacing-10: 40px;
  --spacing-11: 44px;
  --spacing-12: 48px;
  --spacing-14: 56px;
  --spacing-16: 64px;
  --spacing-20: 80px;
  --spacing-24: 96px;
  --spacing-28: 112px;
  --spacing-32: 128px;
}
```

**Note:** Tailwind v4 may already provide a default spacing scale. Check if these tokens are automatically available. If so, only define the SEMANTIC tokens below and let Tailwind handle the base scale. If Tailwind v4's CSS-first config needs explicit registration, use `@theme inline` as shown.

### B. Semantic Spacing Tokens

These are the tokens developers actually reach for. Named by purpose, not by size.

```css
:root {
  /* --- Component-level spacing --- */
  --card-padding: var(--spacing-4);        /* 16px — internal padding for cards, panels */
  --card-gap: var(--spacing-3);            /* 12px — gap between items inside a card */
  --section-gap: var(--spacing-6);         /* 24px — gap between sections within a page */
  --page-gap: var(--spacing-8);            /* 32px — gap between major page regions */
  --sidebar-gap: var(--spacing-6);         /* 24px — gap between sidebar sections */
  --inline-gap: var(--spacing-2);          /* 8px — gap between inline elements (icon + text) */
  --tight-gap: var(--spacing-1);           /* 4px — minimal gap (stacked badges, compact lists) */

  /* --- Row sizing --- */
  --row-height: 44px;                      /* Default interactive row (nav items, list items) */
  --row-height-compact: 36px;              /* Compact row (table rows, dense lists) */

  /* --- Layout dimensions --- */
  --max-width: 1440px;                     /* Dashboard content max-width */
  --nav-height: 56px;                      /* Top navigation bar height */
  --sidebar-width: 256px;                  /* Sidebar expanded width */
  --sidebar-width-collapsed: 48px;         /* Sidebar collapsed (icon-only) width */
  --content-padding: var(--spacing-6);     /* 24px — main content area padding */
}
```

### C. Z-Index Scale

Layered system — each layer has a clear purpose and 10-unit gaps for insertion flexibility.

```css
:root {
  --z-base: 0;          /* Default stacking context */
  --z-sticky: 10;       /* Sticky table headers, pinned elements */
  --z-sidebar: 20;      /* Sidebar overlay (mobile) */
  --z-nav: 30;          /* Top navigation bar */
  --z-dropdown: 40;     /* Dropdowns, select menus, popovers */
  --z-modal: 50;        /* Modal dialogs, sheets */
  --z-toast: 60;        /* Toast notifications (always on top of modals) */
}
```

Register as Tailwind utilities:

```css
@theme inline {
  --z-index-base: 0;
  --z-index-sticky: 10;
  --z-index-sidebar: 20;
  --z-index-nav: 30;
  --z-index-dropdown: 40;
  --z-index-modal: 50;
  --z-index-toast: 60;
}
```

### D. Breakpoints

Courier is a desktop-first AI platform, but must be usable on smaller screens.

```css
/* Tailwind v4 breakpoints — verify these match v4 defaults or customize */
/* sm: 640px — small tablets, large phones */
/* md: 768px — tablets, small laptops */
/* lg: 1024px — laptops — sidebar becomes collapsible here */
/* xl: 1280px — primary target — full dashboard layout */
/* 2xl: 1440px — large monitors — content max-width cap */
```

If Tailwind v4 defaults match these values, no custom config needed. If not, add:

```css
@theme inline {
  --breakpoint-sm: 640px;
  --breakpoint-md: 768px;
  --breakpoint-lg: 1024px;
  --breakpoint-xl: 1280px;
  --breakpoint-2xl: 1440px;
}
```

### E. Motion & Transition Tokens

Spacing and layout transitions need consistent timing. Add these alongside the spacing tokens:

```css
:root {
  /* --- Duration --- */
  --duration-instant: 0ms;
  --duration-fast: 100ms;           /* Hover states, focus rings */
  --duration-normal: 200ms;         /* Most UI transitions */
  --duration-slow: 300ms;           /* Panel open/close, modals */
  --duration-sidebar: 200ms;        /* Sidebar expand/collapse */

  /* --- Easing --- */
  --ease-default: cubic-bezier(0.4, 0, 0.2, 1);    /* General purpose */
  --ease-in: cubic-bezier(0.4, 0, 1, 1);            /* Elements leaving */
  --ease-out: cubic-bezier(0, 0, 0.2, 1);           /* Elements entering */
  --ease-spring: cubic-bezier(0.34, 1.56, 0.64, 1); /* Bouncy — use sparingly */
}
```

## App Shell Component

Build the foundational layout component that wraps every page.

### Architecture

```
┌─────────────────────────────────────────────┐
│ AppShell                                     │
│ ┌──────────┬────────────────────────────────┐│
│ │          │  ┌──────────────────────────┐  ││
│ │          │  │ Header (sticky)          │  ││
│ │ Sidebar  │  ├──────────────────────────┤  ││
│ │ (256px)  │  │                          │  ││
│ │          │  │ Content (scrollable)     │  ││
│ │          │  │                          │  ││
│ │          │  │                          │  ││
│ │          │  └──────────────────────────┘  ││
│ └──────────┴────────────────────────────────┘│
└─────────────────────────────────────────────┘
```

### File: `src/components/layout/app-shell.tsx`

```tsx
// Courier App Shell
// Uses shadcn Sidebar component as base (if available)
// Responsive: sidebar collapses on lg, hides on md and below
```

Requirements for the component:

1. **Check if shadcn Sidebar exists**: Look for `src/components/ui/sidebar.tsx`. If it exists, build on top of it using `SidebarProvider`, `Sidebar`, `SidebarContent`, etc. If not, build a custom implementation.

2. **Sidebar behavior**:
   - Desktop (xl+): Always visible, `--sidebar-width` (256px)
   - Laptop (lg): Collapsible, toggle button in header. Collapsed = `--sidebar-width-collapsed` (48px), icon-only
   - Tablet/Mobile (< lg): Hidden by default, hamburger menu opens as overlay
   - Transition: `--duration-sidebar` with `--ease-default`
   - Background: `var(--color-gray-2)`, border-right: `1px solid var(--color-gray-5)`

3. **Header area** (within main content):
   - Height: `var(--nav-height)` (56px)
   - Sticky: `position: sticky; top: 0; z-index: var(--z-nav)`
   - Background: `var(--color-gray-1)` with subtle border-bottom
   - Contains: sidebar toggle (on lg and below), breadcrumbs slot, actions slot
   - Accept children via props: `headerLeft`, `headerRight`, or use `children` with slots

4. **Content area**:
   - Padding: `var(--content-padding)` on all sides
   - Max-width: `var(--max-width)` centered
   - Overflow-y: auto (this is the scrollable area, NOT the body)
   - Takes `children` prop for page content

5. **Props interface**:
   ```tsx
   interface AppShellProps {
     children: React.ReactNode;           // Main content
     sidebar?: React.ReactNode;           // Sidebar content (nav items, etc.)
     headerLeft?: React.ReactNode;        // Left side of header (breadcrumbs)
     headerRight?: React.ReactNode;       // Right side of header (actions)
     defaultCollapsed?: boolean;          // Start with sidebar collapsed
     className?: string;                  // Additional classes on root
   }
   ```

6. **Data attributes** (for styling hooks):
   - `data-sidebar-state="expanded" | "collapsed" | "hidden"` on root element
   - Components can use `[data-sidebar-state="collapsed"] &` for sidebar-aware layouts

7. **Use design system tokens everywhere**:
   - Colors: `var(--color-gray-*)`, never raw values
   - Spacing: `var(--content-padding)`, `var(--sidebar-gap)`, never raw pixel values
   - Sizing: `var(--sidebar-width)`, `var(--nav-height)`, never hardcoded
   - Z-index: `var(--z-nav)`, `var(--z-sidebar)`, never raw numbers
   - Transitions: `var(--duration-sidebar)`, `var(--ease-default)`

8. **Keyboard accessible**: Sidebar toggle button has `aria-label`, `aria-expanded`. Focus management on open/close. Escape key closes mobile overlay.

## File Outputs

### 1. Update `src/app/globals.css` (or `src/styles/globals.css`)

Add spacing, z-index, and motion sections AFTER typography. Use clear section comments:

```css
/* ============================================
   COURIER DESIGN SYSTEM — Spacing & Layout
   Generated: Step 3 of design system pipeline
   ============================================ */

/* --- Base Spacing Scale (4px grid) --- */
/* ... @theme inline spacing tokens ... */

/* --- Semantic Spacing --- */
/* ... card-padding, section-gap, etc. ... */

/* --- Layout Dimensions --- */
/* ... nav-height, sidebar-width, max-width, etc. ... */

/* --- Z-Index Scale --- */
/* ... z-base through z-toast ... */

/* --- Motion & Transitions --- */
/* ... duration + easing tokens ... */
```

Do NOT touch color or typography sections from Steps 1-2.

### 2. Create `src/components/layout/app-shell.tsx`

The complete, working App Shell component as specified above.

### 3. Create `src/design-system/docs/spacing-layout.md`

```markdown
# Courier Spacing & Layout System

## Base Spacing Scale
| Token | Value | Tailwind |
|-------|-------|---------|
| --spacing-1 | 4px | p-1, m-1, gap-1 |
| --spacing-2 | 8px | p-2, m-2, gap-2 |
| ... | ... | ... |

## Semantic Spacing
| Token | Value | Usage |
|-------|-------|-------|
| --card-padding | 16px | Internal card/panel padding |
| --card-gap | 12px | Gap between items in cards |
| --section-gap | 24px | Gap between page sections |
| --page-gap | 32px | Gap between major page regions |
| --inline-gap | 8px | Icon + text, inline element pairs |
| --tight-gap | 4px | Minimal gaps, compact layouts |

## Layout Dimensions
| Token | Value | Usage |
|-------|-------|-------|
| --nav-height | 56px | Top header bar |
| --sidebar-width | 256px | Sidebar expanded |
| --sidebar-width-collapsed | 48px | Sidebar collapsed (icon-only) |
| --max-width | 1440px | Dashboard content cap |
| --content-padding | 24px | Main content area padding |
| --row-height | 44px | Default interactive row |
| --row-height-compact | 36px | Table rows, dense lists |

## Z-Index Scale
| Token | Value | Purpose |
|-------|-------|---------|
| --z-base | 0 | Default |
| --z-sticky | 10 | Sticky headers |
| --z-sidebar | 20 | Mobile sidebar overlay |
| --z-nav | 30 | Top nav bar |
| --z-dropdown | 40 | Menus, popovers |
| --z-modal | 50 | Dialogs, sheets |
| --z-toast | 60 | Notifications |

## Motion
| Token | Value | Usage |
|-------|-------|-------|
| --duration-fast | 100ms | Hover, focus states |
| --duration-normal | 200ms | Most transitions |
| --duration-slow | 300ms | Panels, modals |
| --ease-default | cubic-bezier(0.4, 0, 0.2, 1) | General purpose |
| --ease-out | cubic-bezier(0, 0, 0.2, 1) | Elements entering |

## Breakpoints
| Name | Value | Layout Behavior |
|------|-------|----------------|
| sm | 640px | Mobile |
| md | 768px | Tablet |
| lg | 1024px | Sidebar becomes collapsible |
| xl | 1280px | Primary target — full layout |
| 2xl | 1440px | Large monitor — content capped |

## App Shell Component
Location: `src/components/layout/app-shell.tsx`

### Usage
\`\`\`tsx
import { AppShell } from '@/components/layout/app-shell'

export default function DashboardLayout({ children }) {
  return (
    <AppShell
      sidebar={<DashboardNav />}
      headerLeft={<Breadcrumbs />}
      headerRight={<UserMenu />}
    >
      {children}
    </AppShell>
  )
}
\`\`\`

### Responsive Behavior
- **xl+**: Sidebar always visible (256px)
- **lg**: Sidebar collapsible via toggle (256px ↔ 48px)
- **< lg**: Sidebar hidden, opens as overlay via hamburger

### Data Attributes
- `data-sidebar-state="expanded"` — sidebar fully open
- `data-sidebar-state="collapsed"` — sidebar icon-only
- `data-sidebar-state="hidden"` — sidebar off-screen (mobile)
```

## Verification Steps

1. **Tokens work**: In the dev server, use `p-4`, `gap-6`, `z-nav` (or equivalent Tailwind v4 syntax) and confirm they resolve correctly
2. **App Shell renders**: Import and render `<AppShell>` in a layout — verify sidebar + header + content area appear
3. **Responsive check**: Resize browser through breakpoints — verify sidebar collapses at lg, hides below md
4. **Sidebar transition**: Toggle sidebar — animation should use `--duration-sidebar` (200ms) with smooth easing
5. **Scroll behavior**: Add enough content to overflow — only the content area should scroll, not the sidebar or header
6. **Z-index layers**: Open a dropdown in the header — it should render above content but below modals
7. **No regressions**: Color and typography from Steps 1-2 still work correctly

## Definition of Done

- [ ] Base spacing scale (4px grid) defined in globals.css
- [ ] Semantic spacing tokens (card-padding, section-gap, etc.) defined and documented
- [ ] Layout dimension tokens (nav-height, sidebar-width, max-width, etc.) defined
- [ ] Z-index scale (base through toast) defined with clear purpose per layer
- [ ] Motion/transition tokens (durations + easing) defined
- [ ] `src/components/layout/app-shell.tsx` exists and renders correctly
- [ ] App Shell is responsive across all breakpoints (sm through 2xl)
- [ ] Sidebar collapses/expands with smooth animation
- [ ] Content area scrolls independently from sidebar and header
- [ ] `src/design-system/docs/spacing-layout.md` documents all tokens and the App Shell component
- [ ] Color tokens (Step 1) and typography tokens (Step 2) are untouched
- [ ] No build errors, no TypeScript errors
- [ ] The design system foundation is complete: color + typography + spacing + layout
