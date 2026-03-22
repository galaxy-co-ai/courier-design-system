# Step 5: Data Components — Courier-Specific

## Purpose

Build the 7 data-display components that power Courier's dashboard and model management surfaces. These are domain-specific compositions built on top of the Step 4 core atoms, designed for real-time metrics, model status, and performance data visualization.

## Dependencies

- **Steps 0-3 complete** — `globals.css` has color tokens (OKLCH), typography scale, spacing system
- **Step 4 complete** — Core components available: Button, Badge, Card, Input, Separator, Skeleton
- **shadcn/ui installed** — primitives available as needed
- **Lucide React installed** — icons (TrendingUp, TrendingDown, MoreHorizontal, etc.)
- **`cn()` utility** at `src/lib/utils.ts`

## Reference Documents

Before writing any code, read:

1. `skills/ui-engineering/SKILL.md` — interaction patterns, hover guards, accessibility
2. `constitution/ENGINEERING-CONSTITUTION.md` — TypeScript strictness, component conventions
3. `src/styles/globals.css` — live token definitions
4. `src/design-system/docs/components-core.md` — Step 4 component APIs (import from these)

## Key Conventions for Data Components

- **Tabular numerals everywhere**: Any component displaying numbers must use `font-variant-numeric: tabular-nums` so values don't jump when updating
- **Signal colors for status**: online/success = `--success`, degraded/warning = `--warning`, offline/error = `--error`
- **HubotSans for hero values**: Large metric numbers use the display font
- **GeneralSans for body/labels**: All supporting text
- **Semantic tokens only**: No raw colors, no arbitrary Tailwind values for anything tokenized

---

## Components to Build

### 1. MetricCard

**File:** `src/components/data/metric-card.tsx`

KPI tile used across dashboard surfaces. Built on the `Card` component from Step 4.

#### Structure

```
┌─────────────────────────┐
│ REQUESTS / HOUR    ↗ +12%│
│ 14,832                   │
│ ▁▂▃▅▆▇█▆▅▃▂▁ (sparkline)│
└─────────────────────────┘
```

| Element | Typography | Color | Notes |
|---------|-----------|-------|-------|
| Label | `type-xs`, uppercase, `letter-spacing: 0.05em` | `--t2` | Describes the metric |
| Value | `type-2xl` or `type-3xl`, HubotSans Black | `--t1` | `tabular-nums`, `lining-nums` |
| Delta | `type-sm`, `font-weight-medium` | `--success` (positive) / `--error` (negative) | Arrow icon (TrendingUp/TrendingDown) + percentage |
| Sparkline | SVG area, 48px tall | `--brand` stroke, `--brand-10` fill | Optional, bottom of card |

Props:

```tsx
interface MetricCardProps extends React.HTMLAttributes<HTMLDivElement> {
  label: string;
  value: string | number;
  delta?: {
    value: number;       // percentage change
    direction: "up" | "down";
  };
  sparkline?: number[];  // array of values for mini chart
  loading?: boolean;     // shows Skeleton when true
  className?: string;
}
```

When `loading` is true, show Skeleton placeholders matching the layout dimensions.

### 2. ModelCard

**File:** `src/components/data/model-card.tsx`

Card representing a single AI model in the model management view. This is Courier's most important card — it must communicate model health at a glance.

#### Structure

```
┌──────────────────────────────────┐
│ ● Llama 3.2 8B                   │
│ STATIC  Q4_K_M  4.2GB            │
│ Latency: 42ms  Throughput: 128t/s │
│                    [Configure] ⋯  │
└──────────────────────────────────┘
```

Status system — left border accent + status dot:

| Status | Dot color | Left border | Dot animation |
|--------|-----------|-------------|---------------|
| online | `--success` | `--success` | none (solid) |
| offline | `--gray-6` | none | none |
| degraded | `--warning` | `--warning` | none |
| starting | `--brand` | `--brand` | `pulse` animation (opacity 1→0.4→1, 2s) |

Driven by `data-status` attribute on root element for CSS-only state changes.

| Element | Typography | Color |
|---------|-----------|-------|
| Model name | `type-md`, `font-weight-semibold` | `--t1` |
| Status dot | 6px circle | signal color per status |
| Config badges | Badge component (from Step 4) | `default` variant |
| Metric values | `type-sm`, `tabular-nums` | `--t2` for labels, `--t1` for values |
| Action buttons | Button `ghost` variant, `sm` size | — |

Hover (with guard): border transitions from `--border` to `--border-mid`.

Overflow menu: three-dot icon button opening a DropdownMenu with actions (Start, Stop, Configure, Remove as destructive).

```tsx
interface ModelCardProps extends React.HTMLAttributes<HTMLDivElement> {
  name: string;
  status: "online" | "offline" | "degraded" | "starting";
  mode: string;           // "STATIC" | "FLEX" etc.
  quantization: string;   // "Q4_K_M" etc.
  size: string;           // "4.2GB" etc.
  latency?: number;       // ms
  throughput?: number;    // tokens/sec
  onConfigure?: () => void;
  onStart?: () => void;
  onStop?: () => void;
  onRemove?: () => void;
  className?: string;
}
```

### 3. DataTable

**File:** `src/components/data/data-table.tsx`

Configurable data table for logs, request history, model lists.

#### Header

- Sticky (`position: sticky`, `top: 0`, `z-index: 10`)
- Typography: `type-xs`, uppercase, `letter-spacing: 0.05em`, `--t3` color
- Background: `--bg` (solid, not transparent — prevents content bleed on scroll)
- Bottom border: `--border`

#### Sorting

- Sortable columns show a chevron icon (ChevronUp/ChevronDown from Lucide)
- Unsorted: `--t3` chevron, sorted: `--t1` chevron
- Click header to cycle: unsorted → ascending → descending → unsorted

#### Rows

- Hover (with guard): `--gray-4` at low alpha background
- Border-bottom: `--border` at 0.5 alpha (subtle)
- Numeric columns: `text-align: right`, `tabular-nums`

#### Density

Controlled via `data-density` attribute on root:
- `compact`: 36px row height, tighter padding
- `comfortable`: 48px row height, standard padding

#### Empty state

When no data: centered message with icon, `type-sm`, `--t3` text. Accepts `emptyState` prop as `React.ReactNode`.

```tsx
interface Column<T> {
  key: keyof T & string;
  label: string;
  sortable?: boolean;
  numeric?: boolean;
  width?: string;         // CSS width value
  render?: (value: T[keyof T], row: T) => React.ReactNode;
}

interface DataTableProps<T extends Record<string, unknown>> extends React.HTMLAttributes<HTMLDivElement> {
  columns: Column<T>[];
  data: T[];
  density?: "compact" | "comfortable";
  emptyState?: React.ReactNode;
  onSort?: (key: string, direction: "asc" | "desc" | null) => void;
  className?: string;
}
```

### 4. ChartContainer

**File:** `src/components/data/chart-container.tsx`

Wrapper for all chart components. Provides consistent framing, title, and controls.

| Element | Styles |
|---------|--------|
| Root | `--elevated` bg, `--border`, `--radius-card`, `p-4` |
| Title | `type-md`, `font-weight-semibold`, `--t1` |
| Time range | Right-aligned, uses TimeRangeToggle (component #6) |
| Chart area | Flex-grow, min-height 200px, responsive |
| Legend | Bottom-positioned, `type-xs`, `--t2`, flex-wrap |

Chart color tokens: `--chart-1` through `--chart-5` (defined in globals.css). These must be used for all multi-series charts.

```tsx
interface ChartContainerProps extends React.HTMLAttributes<HTMLDivElement> {
  title: string;
  timeRange?: React.ReactNode;   // slot for TimeRangeToggle
  legend?: React.ReactNode;      // slot for chart legend
  children: React.ReactNode;     // the chart itself
  className?: string;
}
```

### 5. AreaChart

**File:** `src/components/data/area-chart.tsx`

Time-series area chart for latency, throughput, request volume.

Implementation: pure SVG (no charting library dependency). Keep it simple — Courier's charts are single-series area charts, not complex multi-axis visualizations.

| Element | Style |
|---------|-------|
| Line | 2px stroke, `--brand` (or `--chart-1`) |
| Fill | Linear gradient: `--brand` at 20% opacity → transparent at bottom |
| Grid lines | Horizontal only, `--border` at 0.04 alpha |
| X-axis labels | `type-xs`, `--t3`, monospace/tabular-nums |
| Y-axis labels | `type-xs`, `--t3`, monospace/tabular-nums, right-aligned |
| Tooltip | `--overlay` bg, `--shadow-md`, `type-sm`, `--t1` value |

The chart should be responsive — it fills its container width and respects the height prop.

Tooltip: shown on hover, follows cursor x-position, snaps to nearest data point. Shows formatted value + timestamp.

```tsx
interface DataPoint {
  timestamp: number | string;   // x-axis
  value: number;                // y-axis
}

interface AreaChartProps extends React.HTMLAttributes<HTMLDivElement> {
  data: DataPoint[];
  color?: string;               // CSS variable name, defaults to "--brand"
  height?: number;              // px, defaults to 200
  formatValue?: (value: number) => string;
  formatTimestamp?: (timestamp: number | string) => string;
  showGrid?: boolean;
  showTooltip?: boolean;
  className?: string;
}
```

### 6. TimeRangeToggle

**File:** `src/components/data/time-range-toggle.tsx`

Segmented control for selecting dashboard time ranges: 24h, 7d, 30d, 90d.

Filter pill pattern:

| State | Background | Text | Border |
|-------|-----------|------|--------|
| Inactive | transparent | `type-sm`, `--t2` | `--border-mid` |
| Active | `--t1` (inverted) | `--bg` (inverted) | none |
| Hover (inactive, with guard) | `--gray-4` | `--t1` | `--border-mid` |

Layout: inline-flex, gap-1, pill radius on each item.

**No transition on state switch** — this is a high-frequency interaction. Color change should be instant.

```tsx
interface TimeRangeToggleProps extends React.HTMLAttributes<HTMLDivElement> {
  options?: string[];            // defaults to ["24h", "7d", "30d", "90d"]
  value: string;
  onChange: (value: string) => void;
  className?: string;
}
```

### 7. ProgressBar / CapacityMeter

**File:** `src/components/data/capacity-meter.tsx`

Resource usage display for VRAM, CPU, memory utilization.

| Element | Style |
|---------|-------|
| Track | `--gray-3` bg, `--radius-pill`, 8px height |
| Fill | Animated width transition (`--duration-smooth`), radius matches track |
| Label | `type-sm`, `tabular-nums`, right-aligned above bar |

Fill color logic (driven by value):
- 0-69%: `--brand` (safe)
- 70-89%: `--warning` (caution)
- 90-100%: `--error` (critical)

Color thresholds should be configurable via props.

Optional threshold marker: a thin vertical line on the track at a specific percentage (e.g., 80% recommended max).

```tsx
interface CapacityMeterProps extends React.HTMLAttributes<HTMLDivElement> {
  value: number;                 // 0-100
  label?: string;                // e.g., "VRAM Usage"
  showValue?: boolean;           // show percentage text, defaults true
  warningThreshold?: number;     // defaults to 70
  errorThreshold?: number;       // defaults to 90
  thresholdMarker?: number;      // optional marker line position (0-100)
  formatValue?: (value: number) => string;
  className?: string;
}
```

---

## Output Checklist

- [ ] All 7 components created in `src/components/data/`
- [ ] Every component uses TypeScript `interface` for props
- [ ] Every component accepts `className` prop merged with `cn()`
- [ ] Every component has `data-slot` on root element
- [ ] All numeric displays use `tabular-nums`
- [ ] Status indicators use signal colors (`--success`, `--warning`, `--error`) correctly
- [ ] Large metric values use HubotSans (display font)
- [ ] Interactive elements have hover guards
- [ ] ModelCard uses `data-status` attribute for CSS-driven state
- [ ] DataTable supports both density modes
- [ ] AreaChart is responsive and uses token colors
- [ ] CapacityMeter color thresholds work correctly
- [ ] No raw color values — tokens only
- [ ] Core components (Card, Badge, Button, Skeleton) imported from `src/components/ui/`
- [ ] Create `src/design-system/docs/components-data.md` documenting all 7 components with usage examples and data shape examples

## Definition of Done

A Courier dashboard can be composed from these components with realistic data shapes. MetricCard shows KPIs with deltas. ModelCard communicates model health at a glance across all 4 statuses. DataTable handles sorting, density, and empty states. Charts render time-series data with proper axes and tooltips. CapacityMeter thresholds trigger correct color changes. All numeric values use tabular-nums so columns don't shift. The doc file exists with usage examples and sample data for each component.
