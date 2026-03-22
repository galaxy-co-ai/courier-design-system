# Step 4: Core Components — Foundational Atoms

## Purpose

Build the 10 foundational UI atoms that every Courier surface depends on. These components consume the token system from Steps 0-3 and establish the interaction language for the entire product.

## Dependencies

- **Steps 0-3 complete** — `globals.css` has color tokens (OKLCH), typography scale, spacing system, and layout primitives
- **shadcn/ui installed** — Dialog, Tooltip, DropdownMenu, Switch, Separator primitives available
- **Lucide React installed** — icon library
- **`cn()` utility** exists at `src/lib/utils.ts` (clsx + twMerge)

## Reference Documents

Before writing any code, read:

1. `skills/ui-engineering/SKILL.md` — interaction patterns, hover guards, accessibility baseline
2. `constitution/ENGINEERING-CONSTITUTION.md` — TypeScript strictness, component API conventions
3. `src/styles/globals.css` — the live token definitions (colors, typography, spacing, shadows, radii, durations)

## Interaction Patterns (from Athlete Era)

Apply these universally to every interactive component:

| Pattern | Implementation |
|---------|---------------|
| Hover guard | `@media (hover: hover)` wrapping all `:hover` styles — prevents sticky hover on touch |
| Active press | `transform: scale(0.97)` on `:active` — tactile feedback |
| Focus ring | `box-shadow` using brand color at alpha — not `outline` (allows radius matching) |
| Disabled | `opacity: 0.5`, `pointer-events: none`, no transform on active |
| Tabular nums | `font-variant-numeric: tabular-nums` on all numeric displays |
| Data-attribute states | `data-state`, `data-variant`, `data-size` for CSS-driven state changes |

## Component API Conventions

Every component must:

- Accept `className` prop (merged via `cn()`)
- Use TypeScript `interface` for props (not `type`)
- Export named (not default)
- Use `data-slot` attribute on root element (shadcn convention)
- Use `React.forwardRef` where DOM access is expected
- Support `asChild` pattern where semantics vary (buttons as links, etc.)

---

## Components to Build

### 1. Button

**File:** `src/components/ui/button.tsx`

Variants via `data-variant="primary|ghost|destructive"`, sizes via `data-size="sm|md|lg"`.

| Variant | Background | Text | Height | Border Radius |
|---------|-----------|------|--------|---------------|
| primary | `--brand` | white | 44px | `--radius-md` |
| ghost | transparent | `--t2` | 36px | `--radius-pill` |
| destructive | `--error` | white | 44px | `--radius-md` |

States:
- **Hover** (with guard): primary/destructive lower opacity to 0.88; ghost gets `--border-mid` border
- **Active**: `scale(0.97)` on all variants
- **Focus-visible**: brand-colored box-shadow ring (2px spread, brand at 40% alpha)
- **Disabled**: `opacity: 0.5`, `pointer-events: none`, no scale transform

Loading state:
- Accepts `loading` boolean prop
- When true: content replaced by spinner (16px Loader2 from Lucide, animate-spin)
- Button becomes disabled during loading (no duplicate clicks)
- Width preserved (use `min-width` or invisible content trick)

```tsx
interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: "primary" | "ghost" | "destructive";
  size?: "sm" | "md" | "lg";
  loading?: boolean;
  asChild?: boolean;
  className?: string;
}
```

### 2. Badge

**File:** `src/components/ui/badge.tsx`

Inline status/label element. Variants via `data-variant`.

| Variant | Background | Text |
|---------|-----------|------|
| default | `--gray-4` | `--t2` |
| primary | `--brand-10` (brand at 10% alpha) | `--brand` |
| warning | `--warning-10` | `--warning` |
| success | `--success-10` | `--success` |
| error | `--error-10` | `--error` |
| outline | transparent, `--border` border | `--t2` |

Typography: `type-xs`, `uppercase`, `letter-spacing: 0.05em`, `px-2 py-0.5`.

Status variant: when `status` prop is true, prepend a 6px circle dot colored to match the variant's text color.

```tsx
interface BadgeProps extends React.HTMLAttributes<HTMLSpanElement> {
  variant?: "default" | "primary" | "warning" | "success" | "error" | "outline";
  status?: boolean;
  className?: string;
}
```

### 3. Input

**File:** `src/components/ui/input.tsx`

| Property | Value |
|----------|-------|
| Height | 40px |
| Radius | `--radius-sm` |
| Background | `--input` (filled, not bordered-only) |
| Font size | 16px minimum (prevents iOS auto-zoom) |
| Focus | `--brand` border + `--brand-10` ring |

Variants:
- **text** (default): standard text input
- **search**: Lucide `Search` icon (16px, `--t3`) left-aligned with `pl-10` padding

Icon support: `leftIcon` and `rightIcon` props accept `React.ReactNode` for arbitrary icon placement.

```tsx
interface InputProps extends React.InputHTMLAttributes<HTMLInputElement> {
  variant?: "text" | "search";
  leftIcon?: React.ReactNode;
  rightIcon?: React.ReactNode;
  className?: string;
}
```

### 4. Card

**File:** `src/components/ui/card.tsx`

Base container for all card-like surfaces.

| Property | Value |
|----------|-------|
| Background | `--elevated` |
| Border | `--border` |
| Radius | `--radius-card` |
| Padding | `--space-4` (16px) default |

Interactive variant: when `interactive` prop is true:
- Hover (with guard): border transitions to `--border-mid`, shadow becomes `--shadow-md`
- Active: `scale(0.99)` subtle press
- Cursor: pointer
- Transition: `--duration-fast`

Compound pattern — export sub-components:
- `Card` (root)
- `CardHeader` (flex row, items-center, gap-3)
- `CardTitle` (type-md, font-weight-semibold, --t1)
- `CardDescription` (type-sm, --t2)
- `CardContent` (padding, no top-padding when following header)
- `CardFooter` (flex row, gap-2, border-top, pt-4)

### 5. Modal / Dialog

**File:** `src/components/ui/dialog.tsx`

Extend shadcn Dialog primitive. Do not rebuild from scratch.

| Element | Styles |
|---------|--------|
| Overlay | `--overlay` (black 70%), `backdrop-blur-sm` optional |
| Surface | `--elevated` bg, `--border` border, `--radius-lg`, `--shadow-lg` |
| Close button | top-right, ghost style, 32px hit target |

Sizes via `data-size`:
- `sm`: `max-width: 400px`
- `md`: `max-width: 520px`
- `lg`: `max-width: 640px`

Animation:
- Enter: `--duration-smooth` (300ms), scale from 0.95 + fade
- Exit: `--duration-exit` (150ms), scale to 0.98 + fade

Export: `Dialog`, `DialogTrigger`, `DialogContent`, `DialogHeader`, `DialogTitle`, `DialogDescription`, `DialogFooter`, `DialogClose`.

### 6. Tooltip

**File:** `src/components/ui/tooltip.tsx`

Extend shadcn Tooltip primitive.

| Property | Value |
|----------|-------|
| Background | `--gray-12` (inverted — light on dark) |
| Text | `--gray-1` |
| Typography | `type-xs` |
| Max width | 240px |
| Radius | `--radius-sm` |
| Delay | 300ms (`delayDuration`) |
| Shadow | `--shadow-sm` |

Export: `Tooltip`, `TooltipTrigger`, `TooltipContent`, `TooltipProvider`.

### 7. Dropdown Menu

**File:** `src/components/ui/dropdown-menu.tsx`

Extend shadcn DropdownMenu primitive.

| Element | Styles |
|---------|--------|
| Content | `--elevated` bg, `--border`, `--shadow-md`, `--radius-md` |
| Item | 36px height, `type-sm`, hover `--gray-4` bg |
| Separator | `--border` color, `my-1` |
| Destructive item | `--error` text color, hover `--error-10` bg |

Items should have `px-3` padding and support left icon (20px, `--t3`) + right shortcut text (`type-xs`, `--t3`).

Export: `DropdownMenu`, `DropdownMenuTrigger`, `DropdownMenuContent`, `DropdownMenuItem`, `DropdownMenuSeparator`, `DropdownMenuLabel`, `DropdownMenuGroup`.

### 8. Toggle / Switch

**File:** `src/components/ui/switch.tsx`

Extend shadcn Switch primitive.

| State | Track | Thumb |
|-------|-------|-------|
| Off | `--gray-5` | white |
| On | `--brand` | white |

Thumb has `--shadow-sm`. Transition: `--duration-fast` on track background and thumb position.

Size: 40px wide, 24px tall track; 18px thumb.

Focus: brand box-shadow ring on the track.

```tsx
interface SwitchProps extends React.ComponentPropsWithoutRef<typeof SwitchPrimitive.Root> {
  className?: string;
}
```

### 9. Skeleton

**File:** `src/components/ui/skeleton.tsx`

Loading placeholder with shimmer animation.

CSS custom property: `--skeleton-duration: 1.5s`.

Animation: linear gradient sweep (transparent → white at 8% opacity → transparent) moving left to right, infinite loop.

Base: `--gray-4` background, `--radius-sm`.

Variants via className or dedicated exports:
- `.skeleton` / `Skeleton` — rectangle, accepts `width` and `height` props
- `.skeleton-circle` / `SkeletonCircle` — `border-radius: 50%`, accepts `size` prop
- `.skeleton-pill` / `SkeletonPill` — `--radius-pill`, typical for text lines, accepts `width` and `height`

```tsx
interface SkeletonProps extends React.HTMLAttributes<HTMLDivElement> {
  className?: string;
}
```

### 10. Separator

**File:** `src/components/ui/separator.tsx`

Extend shadcn Separator primitive.

| Property | Value |
|----------|-------|
| Color | `--border` |
| Orientation | horizontal (default) or vertical |
| Horizontal | `h-px w-full` |
| Vertical | `w-px h-full` |

```tsx
interface SeparatorProps extends React.ComponentPropsWithoutRef<typeof SeparatorPrimitive.Root> {
  className?: string;
}
```

---

## Output Checklist

- [ ] All 10 components created in `src/components/ui/`
- [ ] Every component uses TypeScript `interface` for props
- [ ] Every component accepts `className` prop merged with `cn()`
- [ ] Every component has `data-slot` on root element
- [ ] Hover guards (`@media (hover: hover)`) on all hover states
- [ ] Active press `scale(0.97)` on all clickable elements
- [ ] Box-shadow focus rings (not outline) on all focusable elements
- [ ] Disabled state: `opacity: 0.5`, `pointer-events: none`
- [ ] All token references match `globals.css` variable names exactly
- [ ] No raw color values (no `bg-zinc-*`, no `#hex`, no `rgb()`) — tokens only
- [ ] Create `src/design-system/docs/components-core.md` documenting all 10 components with usage examples

## Definition of Done

All 10 components render correctly with the dark theme. All 4 interaction states (hover, active, focus, disabled) work on every interactive component. Hover guards prevent sticky hover on touch devices. Focus is keyboard-accessible. The doc file exists with at least one usage example per component. No TypeScript errors. No raw color values anywhere.
