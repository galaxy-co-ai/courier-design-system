# Step 6: Navigation Components — App Chrome

## Purpose

Build the 5 navigation-layer components that form Courier's app chrome — the persistent structural elements users interact with on every page. These define how users move through the application and orient themselves.

## Dependencies

- **Steps 0-3 complete** — `globals.css` has color tokens (OKLCH), typography scale, spacing system
- **Step 4 complete** — Core components available: Button, Input, Modal/Dialog, Separator, Tooltip
- **Existing shadcn Sidebar** — there is already a sidebar system in place; extend it, do not rebuild
- **Lucide React installed** — nav icons (Home, Settings, ChevronRight, Search, Command, etc.)
- **`cn()` utility** at `src/lib/utils.ts`

## Reference Documents

Before writing any code, read:

1. `skills/ui-engineering/SKILL.md` — interaction patterns, hover guards, accessibility
2. `constitution/ENGINEERING-CONSTITUTION.md` — TypeScript strictness, component conventions
3. `src/styles/globals.css` — live token definitions
4. `src/design-system/docs/components-core.md` — Step 4 component APIs
5. **The existing sidebar code** — read it fully before modifying. Understand what's there.

## Navigation Interaction Principles

- **No animation on content switches** — when a tab/nav item is clicked, content appears instantly. Only indicators (underlines, highlights) may animate.
- **High-frequency interactions get no transitions** — tab text color, breadcrumb state changes are instant.
- **Structural transitions are smooth** — sidebar collapse/expand uses `--duration-smooth` (300ms).
- **Active states are unambiguous** — users must always know where they are. Active items use `--t1` text + a secondary signal (border, background, or indicator).

---

## Components to Build

### 1. Sidebar

**File:** Enhance existing sidebar files (likely `src/components/ui/sidebar.tsx` or similar). If no sidebar exists, create `src/components/nav/sidebar.tsx`.

**Critical: Do not rebuild from scratch.** Read the existing shadcn sidebar implementation first. Extend and style it to match Courier's design tokens.

#### Layout

```
┌──────────────────┐
│ ◆ Courier         │  ← Logo section
│                    │
│ OVERVIEW           │  ← Section label
│ ■ Dashboard        │  ← Active nav item
│ □ Models           │
│ □ Endpoints        │
│                    │
│ MONITOR            │  ← Section label
│ □ Logs             │
│ □ Metrics          │
│                    │
│ ─────────────────  │  ← Separator
│ □ Settings         │
│                    │
│ ○ Jackson Pierce   │  ← User section (bottom-pinned)
└──────────────────┘
```

#### Specifications

| Element | Styles |
|---------|--------|
| Root | `--bg` background, right border `--border`, full viewport height |
| Width | Expanded: 256px, Collapsed: 48px (icon-only) |
| Logo section | `px-4 py-5`, logo mark + wordmark (wordmark hidden when collapsed) |
| Section label | `type-xs`, uppercase, `letter-spacing: 0.05em`, `--t3`, `px-4 mt-6 mb-2` |
| Nav item | 40px height, `type-sm`, `--t2` default, `px-3`, `--radius-sm`, Lucide icon 18px |
| Active item | `--t1` text, `--brand` left border (3px) OR `--gray-4` bg highlight |
| Hover (with guard) | `--gray-4` bg, `--t1` text |
| User section | Bottom-pinned (`mt-auto`), `px-4 py-3`, avatar (28px circle) + name (`type-sm`, `--t1`) |
| Collapse toggle | Bottom area or top, uses ChevronLeft/ChevronRight icon |
| Collapse transition | `--duration-smooth` (300ms), width + content opacity |

When collapsed:
- Only icons visible (centered, 48px container)
- Tooltip on hover showing the full label (use Tooltip from Step 4)
- Section labels hidden
- User section shows avatar only
- Logo shows mark only (no wordmark)

```tsx
interface SidebarProps extends React.HTMLAttributes<HTMLElement> {
  collapsed?: boolean;
  onCollapse?: (collapsed: boolean) => void;
  className?: string;
}

interface SidebarItemProps extends React.HTMLAttributes<HTMLButtonElement> {
  icon: React.ReactNode;
  label: string;
  active?: boolean;
  href?: string;
  className?: string;
}

interface SidebarSectionProps extends React.HTMLAttributes<HTMLDivElement> {
  label: string;
  className?: string;
}

interface SidebarUserProps extends React.HTMLAttributes<HTMLDivElement> {
  name: string;
  avatar?: string;         // URL or initials fallback
  className?: string;
}
```

### 2. TabBar

**File:** `src/components/nav/tab-bar.tsx`

Horizontal tab navigation for switching between views within a page (e.g., Model Detail tabs: Overview | Metrics | Configuration | Logs).

#### Structure

```
  Overview    Metrics    Configuration    Logs
  ────────                                      ← Active indicator (2px)
```

| Element | Styles |
|---------|--------|
| Root | Flex row, bottom border `--border`, gap-0 |
| Tab item | `type-sm`, `px-4 py-3`, `--t2` default, `--t1` active |
| Active indicator | 2px border-bottom, `--t1` color, positioned on active tab |
| Hover (with guard) | `--t1` text (no background change — keep it clean) |

Motion-highlight (optional enhancement): The 2px underline animates horizontally from the previous active tab to the new one using `transform: translateX()` + `width` transition with `--duration-fast`. This is the indicator bar only — text color changes are instant (no transition).

**No transition on tab text color change** — high-frequency interaction.

Controlled component — active tab determined by `value` prop.

```tsx
interface Tab {
  value: string;
  label: string;
  icon?: React.ReactNode;
  disabled?: boolean;
}

interface TabBarProps extends React.HTMLAttributes<HTMLDivElement> {
  tabs: Tab[];
  value: string;
  onChange: (value: string) => void;
  motionHighlight?: boolean;     // enable animated underline, defaults false
  className?: string;
}
```

### 3. Breadcrumbs

**File:** `src/components/nav/breadcrumbs.tsx`

Hierarchical path navigation showing the user's location in the app.

#### Structure

```
🏠 / Models / Llama 3.2 8B / Configuration
```

| Element | Styles |
|---------|--------|
| Root | Flex row, items-center, gap-1.5 |
| Link items | `type-sm`, `--t2`, hover `--t1` (with guard), cursor pointer |
| Current item | `type-sm`, `--t1`, `font-weight-medium`, not clickable |
| Separator | `/` or `ChevronRight` icon (12px), `--t3` |
| Home icon | `Home` from Lucide (16px), `--t2`, first position |

Mobile truncation: When more than 3 items, show: Home / ... / Parent / Current. The `...` is a dropdown (using DropdownMenu from Step 4) that shows the hidden middle items.

```tsx
interface BreadcrumbItem {
  label: string;
  href?: string;           // if absent, treated as current (last item)
  icon?: React.ReactNode;  // optional icon before label
}

interface BreadcrumbsProps extends React.HTMLAttributes<HTMLElement> {
  items: BreadcrumbItem[];
  separator?: "slash" | "chevron";   // defaults to "chevron"
  maxVisible?: number;               // defaults to 4, truncates middle if exceeded
  className?: string;
}
```

Accessibility: Use `<nav aria-label="Breadcrumb">` and `<ol>` semantics.

### 4. PageHeader

**File:** `src/components/nav/page-header.tsx`

Consistent page header used at the top of every page. Provides title, optional subtitle, optional actions, and optional tab bar.

#### Structure

```
┌──────────────────────────────────────────────────┐
│ Models                           [+ Add Model]   │
│ Manage your local AI models                       │
│                                                   │
│ Overview    Performance    Configuration           │ ← Optional TabBar
└──────────────────────────────────────────────────┘
```

| Element | Styles |
|---------|--------|
| Root | `px-6 pt-6 pb-4` (or `pb-0` when tabs present — tabs handle bottom spacing) |
| Title | `type-xl` or `type-2xl`, HubotSans Black, `--t1` |
| Subtitle | `type-md`, `--t2`, `mt-1` |
| Actions | Right-aligned, flex row, gap-2, vertically centered with title |
| Tab bar | Full width below title/subtitle, `mt-4`, no additional horizontal padding |
| Bottom border | `--border` (applied by tab bar if present, or by header if no tabs) |

```tsx
interface PageHeaderProps extends React.HTMLAttributes<HTMLDivElement> {
  title: string;
  subtitle?: string;
  actions?: React.ReactNode;      // right-aligned action buttons
  tabs?: React.ReactNode;         // TabBar component slot
  breadcrumbs?: React.ReactNode;  // Breadcrumbs component slot, rendered above title
  className?: string;
}
```

### 5. CommandPalette (Stretch Goal)

**File:** `src/components/nav/command-palette.tsx`

Global command palette triggered by `Cmd+K` (macOS) / `Ctrl+K` (Windows/Linux). Uses Dialog from Step 4 as the wrapper.

**This is a stretch goal.** Build it if time permits after the first 4 nav components are solid. It adds significant UX value but is not blocking.

#### Structure

```
┌──────────────────────────────────┐
│ 🔍 Search models, commands...    │  ← Search input
│──────────────────────────────────│
│ RECENT                           │
│   Dashboard                      │
│   Llama 3.2 8B                   │
│ COMMANDS                         │
│   Add Model                 ⌘M   │
│   Toggle Theme              ⌘T   │
│ MODELS                           │
│   Llama 3.2 8B         online    │
│   Mistral 7B           offline   │
└──────────────────────────────────┘
```

| Element | Styles |
|---------|--------|
| Wrapper | Dialog `md` size, no standard Dialog header/footer |
| Search input | Top-positioned, 48px height, no border, large text, auto-focus |
| Divider | `--border` below search |
| Group label | `type-xs`, uppercase, `--t3`, `px-3 pt-3 pb-1` |
| Item | 40px height, `type-sm`, `px-3`, hover `--gray-4` bg |
| Shortcut | Right-aligned, `type-xs`, `--t3`, `kbd` styling |
| Active (keyboard) | `--gray-4` bg, `--t1` text |

Keyboard navigation:
- `ArrowUp` / `ArrowDown`: move highlight through items
- `Enter`: select highlighted item
- `Escape`: close palette
- Type to filter: filters all groups simultaneously
- Empty state: "No results found" centered, `--t3`

```tsx
interface CommandItem {
  id: string;
  label: string;
  icon?: React.ReactNode;
  shortcut?: string;        // e.g., "⌘M"
  group: string;             // group label
  onSelect: () => void;
  keywords?: string[];       // additional search terms
}

interface CommandPaletteProps {
  items: CommandItem[];
  open: boolean;
  onOpenChange: (open: boolean) => void;
  placeholder?: string;     // defaults to "Search models, commands..."
  className?: string;
}
```

Global keyboard listener: Register `Cmd+K` / `Ctrl+K` at the layout level. The component itself should accept `open` / `onOpenChange` as controlled props. Provide a `useCommandPalette` hook or context for triggering from anywhere.

---

## Output Checklist

- [ ] Sidebar extended (not rebuilt) with Courier design tokens
- [ ] Sidebar collapse/expand works with smooth transition
- [ ] Collapsed sidebar shows tooltips on icon hover
- [ ] TabBar renders with instant text color changes (no transition)
- [ ] TabBar optional motion-highlight animates indicator bar only
- [ ] Breadcrumbs truncate on mobile with dropdown for hidden items
- [ ] Breadcrumbs use semantic HTML (`nav`, `ol`)
- [ ] PageHeader composes with TabBar and Breadcrumbs slots
- [ ] PageHeader actions are right-aligned and vertically centered
- [ ] All hover states use hover guards (`@media (hover: hover)`)
- [ ] All focusable elements have box-shadow focus rings
- [ ] No raw color values — tokens only
- [ ] Every component accepts `className` prop merged with `cn()`
- [ ] Every component has `data-slot` on root element
- [ ] TypeScript interfaces for all props
- [ ] CommandPalette (stretch): keyboard navigation works, search filters all groups
- [ ] Create `src/design-system/docs/components-nav.md` documenting all components with usage examples

## Definition of Done

Complete navigation chrome. The sidebar collapses and expands smoothly, showing tooltips when collapsed. Tabs switch views with instant feedback. Breadcrumbs show location and handle overflow. Page headers are consistent across all pages with proper title hierarchy. All navigation components work together — a user can navigate the full app using sidebar, tabs, breadcrumbs, and (optionally) the command palette. Active states are always unambiguous. The doc file exists with usage examples for each component. No TypeScript errors. No raw color values.
