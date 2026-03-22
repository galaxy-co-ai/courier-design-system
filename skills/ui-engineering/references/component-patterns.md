# Component Patterns — Courier UI Engineering Reference

> Battle-tested component patterns for Courier AI infrastructure dashboard.
> Stack: Dark-first, Tailwind v4, shadcn/ui, React 19, Next.js 16.
> Every pattern is implementation-ready with token mappings, state definitions, and code structure.

---

## Table of Contents

1. [Metric Card (KPI Tile)](#1-metric-card-kpi-tile)
2. [Model Card](#2-model-card)
3. [Data Table](#3-data-table)
4. [Chart Container](#4-chart-container)
5. [Sidebar](#5-sidebar)
6. [Tab Bar](#6-tab-bar)
7. [Badge](#7-badge)
8. [Button](#8-button)
9. [Input](#9-input)
10. [Modal / Dialog](#10-modal--dialog)
11. [Tooltip](#11-tooltip)
12. [Dropdown Menu](#12-dropdown-menu)
13. [API Config Display](#13-api-config-display)
14. [Status Indicator](#14-status-indicator)
15. [Capacity Meter](#15-capacity-meter)

---

## Shared Conventions

### Hover Guard

Every hover style wraps in `@media (hover: hover)` to prevent sticky-hover on touch:

```css
@media (hover: hover) {
  .card:hover { border-color: var(--border-mid); }
}
```

### Data-Attribute States

Prefer `data-*` attributes over conditional class strings for states:

```tsx
<div data-status="online" data-density="compact" className="model-card">
```

```css
.model-card[data-status="online"]  { border-left-color: var(--success); }
.model-card[data-status="degraded"] { border-left-color: var(--warning); }
.model-card[data-density="compact"] { --row-height: 36px; }
```

### Transition Token Classes

```css
.transition-colors { transition: color, background-color, border-color var(--duration-fast) var(--ease-standard); }
.transition-transform { transition: transform var(--duration-fast) var(--ease-standard); }
.transition-opacity { transition: opacity var(--duration-fast) var(--ease-standard); }
.transition-smooth { transition: all var(--duration-smooth) var(--ease-standard); }
```

### Tabular Numbers

All numeric values — metrics, percentages, latencies, counts — use `font-variant-numeric: tabular-nums` to prevent layout shift during live updates.

---

## 1. Metric Card (KPI Tile)

### Anatomy

```
┌─────────────────────────┐
│ THROUGHPUT          ↗ ▲ │  ← uppercase tracked label + trend icon
│ 1,247 req/s            │  ← large value (HubotSans, tabular-nums)
│ +12.3% vs 24h ago      │  ← delta (success/error color)
│ ░░░░░░░░░░░░░░░ spark  │  ← optional sparkline
└─────────────────────────┘
```

### Token Usage

| Element        | Token / Class                                          |
|----------------|--------------------------------------------------------|
| Container bg   | `var(--bg-elevated)`                                   |
| Container border | `1px solid var(--border)`                            |
| Container radius | `var(--radius-card)`                                 |
| Label text     | `type-xs`, `uppercase`, `tracking-wide`, `var(--t2)`   |
| Value text     | `type-3xl` (primary) or `type-2xl` (compact), `var(--t1)`, `tabular-nums` |
| Delta positive | `type-sm`, `var(--success)`, `tabular-nums`            |
| Delta negative | `type-sm`, `var(--error)`, `tabular-nums`              |
| Sparkline      | Stroke: `var(--brand)`, Fill: `var(--brand)` at 10% alpha |
| Trend icon     | `16px`, `var(--t3)` default, `var(--success)` if positive |
| Padding        | `var(--space-4)` all sides (16px)                      |

### Interaction States

| State    | Visual Change                                        |
|----------|------------------------------------------------------|
| Default  | `bg-elevated`, `border`                              |
| Hover    | `border-color: var(--border-mid)` (hover guard)      |
| Active   | `scale(0.98)`, `duration-fast`                       |
| Disabled | `opacity: 0.5`, `pointer-events: none`               |

### Courier-Specific Notes

- Values auto-format with locale-aware number formatting (commas, decimals).
- Delta comparison window is configurable: 1h, 6h, 24h, 7d.
- Sparkline data is a `number[]` of the last 24 data points; no axes, no labels.
- Cards use CSS Grid inside a responsive container: `grid-template-columns: repeat(auto-fill, minmax(240px, 1fr))`.
- When a metric has no data yet, show a `--` placeholder in `var(--t3)`, no sparkline.

### Code Pattern

```tsx
interface MetricCardProps {
  label: string;
  value: string;
  delta?: {
    value: number;      // e.g. 12.3
    direction: "up" | "down" | "flat";
    window: string;     // e.g. "24h ago"
  };
  sparkData?: number[];
  icon?: React.ReactNode;
  onClick?: () => void;
}

function MetricCard({ label, value, delta, sparkData, icon, onClick }: MetricCardProps) {
  return (
    <button
      type="button"
      onClick={onClick}
      disabled={!onClick}
      data-has-spark={!!sparkData}
      className={cn(
        // layout
        "flex flex-col gap-1 p-4 text-left w-full",
        // surface
        "bg-elevated border border-border rounded-card",
        // transitions
        "transition-colors duration-fast ease-standard",
        // states (hover guard applied via CSS)
        "hover-guard:hover:border-border-mid",
        "active:scale-[0.98]",
        "disabled:opacity-50 disabled:pointer-events-none",
      )}
    >
      {/* Header row: label + icon */}
      <div className="flex items-center justify-between">
        <span className="type-xs uppercase tracking-wide text-t2">
          {label}
        </span>
        {icon && (
          <span className="text-t3 size-4">{icon}</span>
        )}
      </div>

      {/* Value */}
      <span className="type-3xl font-heading text-t1 tabular-nums">
        {value}
      </span>

      {/* Delta */}
      {delta && (
        <span
          data-direction={delta.direction}
          className={cn(
            "type-sm tabular-nums",
            "data-[direction=up]:text-success",
            "data-[direction=down]:text-error",
            "data-[direction=flat]:text-t3",
          )}
        >
          {delta.direction === "up" ? "+" : delta.direction === "down" ? "-" : ""}
          {Math.abs(delta.value)}% vs {delta.window}
        </span>
      )}

      {/* Sparkline */}
      {sparkData && (
        <Sparkline data={sparkData} className="h-6 w-full mt-1" />
      )}
    </button>
  );
}
```

---

## 2. Model Card

### Anatomy

```
┌─────────────────────────────────┐
│ ● mistral-7b-instruct-v0.3     │  ← status dot + model name
│ Q4_K_M · STATIC · 4.2GB        │  ← config badges
│                                 │
│ Latency: 23ms    Throughput: 87 │  ← metrics row
│ [Configure]  [···]              │  ← actions
└─────────────────────────────────┘
```

### Token Usage

| Element        | Token / Class                                              |
|----------------|-------------------------------------------------------------|
| Container bg   | `var(--bg-elevated)`                                        |
| Container border | `1px solid var(--border)`                                 |
| Left accent    | `3px solid` — color set by `data-status`                    |
| Status dot     | `6px` circle, inline, signal color                          |
| Model name     | `type-md`, `var(--t1)`, `font-heading`                      |
| Badges         | `type-xs`, `uppercase`, `tracking-wide`, alpha bg variant   |
| Metrics label  | `type-sm`, `var(--t2)`                                      |
| Metrics value  | `type-sm`, `var(--t1)`, `tabular-nums`                      |
| Dot separator  | `·` character in `var(--t3)`                                |
| Padding        | `var(--space-4)` (16px)                                     |

### Interaction States

| State    | Visual Change                                                  |
|----------|----------------------------------------------------------------|
| Default  | `bg-elevated`, `border`, left accent per status                |
| Hover    | `border-color: var(--border-mid)` (hover guard)                |
| Active   | `scale(0.99)`                                                  |
| Disabled | `opacity: 0.5`, `pointer-events: none`, no left accent        |

### Data-Attribute Styling

```css
.model-card { border-left: 3px solid transparent; }
.model-card[data-status="online"]   { border-left-color: var(--success); }
.model-card[data-status="offline"]  { border-left-color: var(--error); }
.model-card[data-status="degraded"] { border-left-color: var(--warning); }
.model-card[data-status="loading"]  { border-left-color: var(--brand); }
```

### Courier-Specific Notes

- Model names can be long — truncate with `text-ellipsis overflow-hidden whitespace-nowrap`, full name in tooltip.
- Badge separator uses the `·` (middle dot) character, spaced `gap-1.5`.
- Configure button is `Ghost` variant; overflow menu (`···`) is icon-only ghost button.
- Cards lay out in a CSS Grid: `repeat(auto-fill, minmax(320px, 1fr))`.
- When a model is loading, the status dot pulses (see [Status Indicator](#14-status-indicator)).

### Code Pattern

```tsx
type ModelStatus = "online" | "offline" | "degraded" | "loading";

interface ModelCardProps {
  name: string;
  status: ModelStatus;
  quant: string;          // e.g. "Q4_K_M"
  mode: string;           // e.g. "STATIC" | "FLEX"
  size: string;           // e.g. "4.2GB"
  metrics: {
    latency: string;      // e.g. "23ms"
    throughput: string;    // e.g. "87 req/s"
  };
  onConfigure: () => void;
  onMenuAction: (action: string) => void;
}

function ModelCard({
  name, status, quant, mode, size, metrics, onConfigure, onMenuAction,
}: ModelCardProps) {
  return (
    <div
      data-status={status}
      className={cn(
        "flex flex-col gap-3 p-4",
        "bg-elevated border border-border rounded-card",
        "border-l-3 transition-colors duration-fast ease-standard",
        // left accent set by data-status in CSS
        "hover-guard:hover:border-border-mid",
      )}
    >
      {/* Header: status dot + name */}
      <div className="flex items-center gap-2 min-w-0">
        <StatusDot status={status} />
        <span className="type-md font-heading text-t1 truncate">
          {name}
        </span>
      </div>

      {/* Config badges */}
      <div className="flex items-center gap-1.5 flex-wrap">
        <Badge variant="default">{quant}</Badge>
        <span className="text-t3">·</span>
        <Badge variant="default">{mode}</Badge>
        <span className="text-t3">·</span>
        <Badge variant="default">{size}</Badge>
      </div>

      {/* Metrics row */}
      <div className="flex items-center gap-6">
        <MetricInline label="Latency" value={metrics.latency} />
        <MetricInline label="Throughput" value={metrics.throughput} />
      </div>

      {/* Actions */}
      <div className="flex items-center gap-2 pt-1">
        <Button variant="ghost" size="sm" onClick={onConfigure}>
          Configure
        </Button>
        <DropdownMenu>
          <DropdownMenuTrigger asChild>
            <Button variant="ghost" size="icon-sm" aria-label="More actions">
              <MoreHorizontal className="size-4" />
            </Button>
          </DropdownMenuTrigger>
          {/* menu content */}
        </DropdownMenu>
      </div>
    </div>
  );
}

function MetricInline({ label, value }: { label: string; value: string }) {
  return (
    <div className="flex items-baseline gap-1.5">
      <span className="type-sm text-t2">{label}:</span>
      <span className="type-sm text-t1 tabular-nums">{value}</span>
    </div>
  );
}
```

---

## 3. Data Table

### Anatomy

```
┌────────┬──────────┬─────────┬────────┐
│ MODEL  │ STATUS   │ LATENCY │ REQS   │  ← sticky header, type-xs, uppercase
├────────┼──────────┼─────────┼────────┤
│ row    │ ● Online │ 23ms    │ 1,247  │  ← alternating subtle bg
│ row    │ ○ Idle   │ --      │ 0      │
└────────┴──────────┴─────────┴────────┘
```

### Token Usage

| Element          | Token / Class                                             |
|------------------|-----------------------------------------------------------|
| Container        | `bg-elevated`, `border`, `rounded-card`, `overflow-hidden` |
| Header bg        | `var(--bg-surface)` or `var(--gray-2)`                    |
| Header text      | `type-xs`, `var(--t3)`, `uppercase`, `tracking-wide`      |
| Header height    | `44px`                                                     |
| Cell text (val)  | `type-sm`, `var(--t1)`                                    |
| Cell text (label)| `type-sm`, `var(--t2)`                                    |
| Numeric cells    | `text-right`, `tabular-nums`, `font-mono`                 |
| Row border       | `1px solid var(--border)` on bottom                       |
| Alt row bg       | `var(--gray-2)` at `0.3` alpha (even rows)                |
| Row hover bg     | `var(--gray-4)` at `0.15` alpha                           |
| Compact row h    | `36px`                                                     |
| Comfortable row h| `48px`                                                     |
| Sort arrow       | `var(--t3)` default, `var(--t1)` when active               |
| Padding          | Cells: `px-3` horizontal                                  |

### Interaction States

| State         | Visual Change                                              |
|---------------|-------------------------------------------------------------|
| Default       | Alternating row bg                                          |
| Row hover     | `bg: var(--gray-4) / 0.15` (hover guard)                   |
| Row selected  | `bg: var(--brand) / 0.08`, `border-left: 2px solid brand`  |
| Header hover  | Sortable columns: cursor pointer, arrow opacity → 1        |
| Disabled row  | `opacity: 0.5`                                              |

### Data-Attribute Styling

```css
.data-table[data-density="compact"]     { --row-height: 36px; }
.data-table[data-density="comfortable"] { --row-height: 48px; }

.data-table tr { height: var(--row-height); }

.data-table th[data-sortable] { cursor: pointer; }
.data-table th[data-sort-dir="asc"]  .sort-arrow { rotate: 0deg; }
.data-table th[data-sort-dir="desc"] .sort-arrow { rotate: 180deg; }
```

### Courier-Specific Notes

- Tables always scroll horizontally on mobile — wrap in `overflow-x-auto`.
- Status columns render a `StatusDot` + label, not plain text.
- Numeric columns (latency, req count) are right-aligned with `tabular-nums`.
- Empty state: full-width centered message, `type-sm`, `var(--t3)`, with optional icon.
- Sticky header uses `position: sticky; top: 0; z-index: 10;` with `bg-surface` to prevent transparency bleed.
- Sortable columns show a small arrow icon (`ChevronUp` from Lucide, 12px).

### Code Pattern

```tsx
interface Column<T> {
  key: keyof T & string;
  label: string;
  sortable?: boolean;
  align?: "left" | "right";
  render?: (value: T[keyof T], row: T) => React.ReactNode;
}

interface DataTableProps<T extends { id: string }> {
  columns: Column<T>[];
  data: T[];
  density?: "compact" | "comfortable";
  sortKey?: string;
  sortDir?: "asc" | "desc";
  onSort?: (key: string) => void;
  onRowClick?: (row: T) => void;
  emptyMessage?: string;
}

function DataTable<T extends { id: string }>({
  columns, data, density = "comfortable",
  sortKey, sortDir, onSort, onRowClick, emptyMessage,
}: DataTableProps<T>) {
  return (
    <div
      data-density={density}
      className="data-table bg-elevated border border-border rounded-card overflow-hidden"
    >
      <div className="overflow-x-auto">
        <table className="w-full border-collapse">
          <thead>
            <tr className="bg-surface h-11">
              {columns.map((col) => (
                <th
                  key={col.key}
                  data-sortable={col.sortable || undefined}
                  data-sort-dir={sortKey === col.key ? sortDir : undefined}
                  onClick={() => col.sortable && onSort?.(col.key)}
                  className={cn(
                    "px-3 type-xs text-t3 uppercase tracking-wide font-normal",
                    "sticky top-0 z-10 bg-surface",
                    col.align === "right" ? "text-right" : "text-left",
                    col.sortable && "cursor-pointer select-none",
                  )}
                >
                  <span className="inline-flex items-center gap-1">
                    {col.label}
                    {col.sortable && (
                      <ChevronUp
                        className={cn(
                          "size-3 sort-arrow transition-transform duration-fast",
                          sortKey !== col.key && "opacity-0",
                        )}
                      />
                    )}
                  </span>
                </th>
              ))}
            </tr>
          </thead>
          <tbody>
            {data.length === 0 ? (
              <tr>
                <td colSpan={columns.length} className="px-3 py-8 text-center type-sm text-t3">
                  {emptyMessage ?? "No data available"}
                </td>
              </tr>
            ) : (
              data.map((row, i) => (
                <tr
                  key={row.id}
                  onClick={() => onRowClick?.(row)}
                  className={cn(
                    "h-[var(--row-height)] border-b border-border",
                    "transition-colors duration-fast ease-standard",
                    i % 2 === 1 && "bg-gray-2/30",
                    "hover-guard:hover:bg-gray-4/15",
                    onRowClick && "cursor-pointer",
                  )}
                >
                  {columns.map((col) => (
                    <td
                      key={col.key}
                      className={cn(
                        "px-3 type-sm text-t1",
                        col.align === "right" && "text-right tabular-nums font-mono",
                      )}
                    >
                      {col.render
                        ? col.render(row[col.key], row)
                        : String(row[col.key] ?? "--")}
                    </td>
                  ))}
                </tr>
              ))
            )}
          </tbody>
        </table>
      </div>
    </div>
  );
}
```

---

## 4. Chart Container

### Anatomy

```
┌─────────────────────────────────────┐
│ Throughput Over Time     [24h ▾]    │  ← title + time range selector
│                                     │
│    ╱╲    ╱╲                        │
│   ╱  ╲  ╱  ╲   ╱╲                 │  ← area chart with gradient fill
│  ╱    ╲╱    ╲ ╱  ╲                │
│ ╱              ╲                   │
│ ──────────────────────────────────  │  ← x-axis
│ 00:00  04:00  08:00  12:00  16:00  │
└─────────────────────────────────────┘
```

### Token Usage

| Element          | Token / Class                                         |
|------------------|-------------------------------------------------------|
| Container bg     | `var(--bg-elevated)`                                  |
| Container border | `1px solid var(--border)`                             |
| Container radius | `var(--radius-card)`                                  |
| Title            | `type-md`, `var(--t1)`                                |
| Time selector    | Ghost button, `type-sm`, `var(--t2)`                  |
| Chart line       | `stroke: var(--brand)`, `stroke-width: 2`             |
| Chart fill       | `var(--brand)` gradient from 20% alpha → 0% alpha     |
| Grid lines       | `var(--border)` at `0.04` alpha, `stroke-width: 1`    |
| Axis labels      | `font-mono`, `type-xs`, `var(--t3)`                   |
| Tooltip bg       | `var(--bg-overlay)`                                   |
| Tooltip text     | `type-sm`, `var(--t1)`                                |
| Tooltip shadow   | `var(--shadow-md)`                                    |
| Padding          | `var(--space-4)` for header, chart area has own inset  |

### Interaction States

| State     | Visual Change                                              |
|-----------|-------------------------------------------------------------|
| Default   | Chart rendered, no crosshair                                |
| Hover     | Vertical crosshair line (`var(--border-mid)`, dashed), tooltip appears at data point |
| Active    | Click-to-pin tooltip (toggle)                               |
| Loading   | Skeleton pulse over chart area, title visible               |
| Empty     | Centered "No data for this period" message in `var(--t3)`   |

### Courier-Specific Notes

- Use Recharts or Tremor as the charting library — both work with shadcn.
- Gradient fill uses a `<linearGradient>` from `brand/20` at top to `brand/0` at bottom.
- Grid lines are extremely subtle — `0.04` alpha prevents visual noise.
- Time range selector options: 1h, 6h, 24h, 7d, 30d.
- Tooltip follows cursor horizontally, snaps to nearest data point vertically.
- Chart container has a `min-height: 200px` to prevent collapse.
- Responsive: below 640px, reduce x-axis label count by half.

### Code Pattern

```tsx
interface ChartContainerProps {
  title: string;
  timeRange: "1h" | "6h" | "24h" | "7d" | "30d";
  onTimeRangeChange: (range: string) => void;
  children: React.ReactNode;  // the actual chart (Recharts/Tremor)
  loading?: boolean;
  empty?: boolean;
}

function ChartContainer({
  title, timeRange, onTimeRangeChange, children, loading, empty,
}: ChartContainerProps) {
  return (
    <div className="bg-elevated border border-border rounded-card overflow-hidden">
      {/* Header */}
      <div className="flex items-center justify-between px-4 pt-4 pb-2">
        <h3 className="type-md text-t1">{title}</h3>
        <TimeRangeSelect value={timeRange} onChange={onTimeRangeChange} />
      </div>

      {/* Chart area */}
      <div className="px-2 pb-4 min-h-[200px] relative">
        {loading ? (
          <div className="absolute inset-0 flex items-center justify-center">
            <div className="h-full w-full bg-surface animate-pulse rounded" />
          </div>
        ) : empty ? (
          <div className="absolute inset-0 flex items-center justify-center">
            <span className="type-sm text-t3">No data for this period</span>
          </div>
        ) : (
          children
        )}
      </div>
    </div>
  );
}

/* Chart gradient definition (place inside SVG defs) */
const chartGradient = (
  <defs>
    <linearGradient id="brandFill" x1="0" y1="0" x2="0" y2="1">
      <stop offset="0%" stopColor="var(--brand)" stopOpacity={0.2} />
      <stop offset="100%" stopColor="var(--brand)" stopOpacity={0} />
    </linearGradient>
  </defs>
);
```

---

## 5. Sidebar

### Anatomy

```
┌──────────────────┐
│ [Logo] Courier   │  ← logo + product name
│                  │
│ ◈ Dashboard      │  ← nav items (Lucide icons)
│ ◈ Workbench      │
│ ◈ Analytics      │
│ ◈ API Keys       │
│ ─────────────    │  ← separator
│ ◈ Settings       │
│                  │
│ ┌──────────────┐ │
│ │ 👤 Jackson   │ │  ← user section, bottom-pinned
│ └──────────────┘ │
└──────────────────┘
```

### Token Usage

| Element          | Token / Class                                              |
|------------------|------------------------------------------------------------|
| Sidebar bg       | `var(--sidebar)` or `var(--gray-2)` — darker than content   |
| Width expanded   | `256px`                                                     |
| Width collapsed  | `48px` (icon-only)                                          |
| Logo text        | `type-md`, `font-heading`, `var(--t1)`                      |
| Section label    | `type-xs`, `uppercase`, `tracking-wide`, `var(--t3)`        |
| Nav item         | `type-sm`, `var(--t2)`, icon + label, `40px` height         |
| Nav icon         | `size-4` (16px), `var(--t3)`                                |
| Active item text | `var(--t1)`                                                 |
| Active indicator | `border-left: 2px solid var(--brand)` or `bg: var(--brand)/0.1` |
| Separator        | `1px solid var(--border)`, `mx-3`, `my-2`                   |
| User section     | Bottom-pinned, `border-top: 1px solid var(--border)`        |
| User avatar      | `28px` circle, `bg-surface`                                 |
| Transition       | `width var(--duration-smooth) var(--ease-standard)`          |

### Interaction States

| State     | Visual Change                                                |
|-----------|--------------------------------------------------------------|
| Default   | `text-t2`, icon `text-t3`                                    |
| Hover     | `bg: var(--gray-4)/0.15`, `text-t1` (hover guard)           |
| Active    | `text-t1`, left border or bg highlight, icon `text-brand`    |
| Collapsed | Icons only, tooltip on hover with nav label                  |
| Mobile    | Overlay panel with `bg-overlay` backdrop, slide-in from left |

### Courier-Specific Notes

- Sidebar collapse is controlled by a `data-collapsed` attribute on the sidebar root.
- On collapse, text fades out (`opacity 0, duration-fast`) before width shrinks.
- On expand, width grows first, text fades in after (`delay 100ms`).
- User section shows avatar + name expanded, avatar-only collapsed.
- Badge counts (e.g., unread notifications) use the `Badge` component, `variant="primary"`, positioned right.
- Mobile breakpoint: below `768px`, sidebar becomes a sheet overlay.

### Code Pattern

```tsx
interface SidebarProps {
  collapsed: boolean;
  onToggleCollapse: () => void;
  activeRoute: string;
  user: { name: string; avatar?: string };
}

interface NavItem {
  label: string;
  href: string;
  icon: LucideIcon;
  badge?: number;
  section?: string;
}

const navItems: NavItem[] = [
  { label: "Dashboard",  href: "/",           icon: LayoutDashboard },
  { label: "Workbench",  href: "/workbench",  icon: Cpu },
  { label: "Analytics",  href: "/analytics",  icon: BarChart3 },
  { label: "API Keys",   href: "/api-keys",   icon: Key },
  { label: "Settings",   href: "/settings",   icon: Settings, section: "divider" },
];

function Sidebar({ collapsed, onToggleCollapse, activeRoute, user }: SidebarProps) {
  return (
    <aside
      data-collapsed={collapsed || undefined}
      className={cn(
        "flex flex-col h-screen bg-sidebar border-r border-border",
        "transition-[width] duration-smooth ease-standard",
        collapsed ? "w-12" : "w-64",
      )}
    >
      {/* Logo */}
      <div className="flex items-center gap-2 h-14 px-3 shrink-0">
        <CourierLogo className="size-6 shrink-0" />
        {!collapsed && (
          <span className="type-md font-heading text-t1 animate-in fade-in duration-fast">
            Courier
          </span>
        )}
      </div>

      {/* Navigation */}
      <nav className="flex-1 overflow-y-auto py-2">
        {navItems.map((item) => (
          <Fragment key={item.href}>
            {item.section === "divider" && (
              <div className="border-t border-border mx-3 my-2" />
            )}
            <NavLink
              item={item}
              active={activeRoute === item.href}
              collapsed={collapsed}
            />
          </Fragment>
        ))}
      </nav>

      {/* User section */}
      <div className="shrink-0 border-t border-border p-2">
        <div className="flex items-center gap-2 px-2 py-1.5 rounded-md">
          <Avatar className="size-7 shrink-0">
            <AvatarImage src={user.avatar} />
            <AvatarFallback className="type-xs">{user.name[0]}</AvatarFallback>
          </Avatar>
          {!collapsed && (
            <span className="type-sm text-t1 truncate">{user.name}</span>
          )}
        </div>
      </div>
    </aside>
  );
}

function NavLink({
  item, active, collapsed,
}: { item: NavItem; active: boolean; collapsed: boolean }) {
  const content = (
    <Link
      href={item.href}
      data-active={active || undefined}
      className={cn(
        "flex items-center gap-2 h-10 px-3 mx-1 rounded-md",
        "type-sm transition-colors duration-fast ease-standard",
        "text-t2 hover-guard:hover:bg-gray-4/15 hover-guard:hover:text-t1",
        "data-[active]:text-t1 data-[active]:bg-brand/10",
        "data-[active]:border-l-2 data-[active]:border-brand",
      )}
    >
      <item.icon className={cn("size-4 shrink-0", active ? "text-brand" : "text-t3")} />
      {!collapsed && <span className="truncate">{item.label}</span>}
      {!collapsed && item.badge && (
        <Badge variant="primary" className="ml-auto">{item.badge}</Badge>
      )}
    </Link>
  );

  if (collapsed) {
    return (
      <Tooltip>
        <TooltipTrigger asChild>{content}</TooltipTrigger>
        <TooltipContent side="right">{item.label}</TooltipContent>
      </Tooltip>
    );
  }

  return content;
}
```

---

## 6. Tab Bar

### Anatomy

```
┌─────────────────────────────────────┐
│ [All] [Running] [Stopped] [Library] │
│  ▬▬▬                                │  ← animated underline (motion-highlight)
└─────────────────────────────────────┘
```

### Token Usage

| Element           | Token / Class                                    |
|-------------------|--------------------------------------------------|
| Container         | `border-bottom: 1px solid var(--border)`          |
| Tab text          | `type-sm`, `var(--t2)`                            |
| Active tab text   | `var(--t1)`                                       |
| Underline         | `2px solid var(--t1)`, animated position          |
| Tab padding       | `px-3 py-2`                                       |
| Tab gap           | `gap-0` (tabs butt up against each other)         |

### Interaction States

| State    | Visual Change                                           |
|----------|---------------------------------------------------------|
| Default  | `text-t2`, no underline                                 |
| Hover    | `text-t1` (hover guard), NO transition on text change   |
| Active   | `text-t1`, underline slides to this tab                 |
| Disabled | `opacity: 0.5`, `pointer-events: none`                  |

### Courier-Specific Notes

- The underline indicator animates its `left` and `width` to match the active tab. This is the **only** animation; tab content switches instantly with no fade/slide.
- Use `framer-motion`'s `layoutId` or a manual `useRef` measurement approach for the sliding indicator.
- Tabs are high-frequency interaction — never add transition delays to the text color. The indicator animation is enough feedback.
- Tab counts (e.g., "Running (3)") are common — count renders in `var(--t3)` after the label.

### Code Pattern

```tsx
interface Tab {
  id: string;
  label: string;
  count?: number;
  disabled?: boolean;
}

interface TabBarProps {
  tabs: Tab[];
  activeTab: string;
  onTabChange: (id: string) => void;
}

function TabBar({ tabs, activeTab, onTabChange }: TabBarProps) {
  const tabRefs = useRef<Map<string, HTMLButtonElement>>(new Map());
  const [indicator, setIndicator] = useState({ left: 0, width: 0 });

  // Measure active tab for indicator position
  useEffect(() => {
    const el = tabRefs.current.get(activeTab);
    if (el) {
      setIndicator({ left: el.offsetLeft, width: el.offsetWidth });
    }
  }, [activeTab]);

  return (
    <div className="relative border-b border-border">
      <div className="flex" role="tablist">
        {tabs.map((tab) => (
          <button
            key={tab.id}
            ref={(el) => { if (el) tabRefs.current.set(tab.id, el); }}
            role="tab"
            aria-selected={tab.id === activeTab}
            disabled={tab.disabled}
            onClick={() => onTabChange(tab.id)}
            className={cn(
              "px-3 py-2 type-sm whitespace-nowrap",
              "disabled:opacity-50 disabled:pointer-events-none",
              tab.id === activeTab ? "text-t1" : "text-t2",
              "hover-guard:hover:text-t1",
              // NO transition on color — high-frequency
            )}
          >
            {tab.label}
            {tab.count !== undefined && (
              <span className="ml-1.5 text-t3">({tab.count})</span>
            )}
          </button>
        ))}
      </div>

      {/* Animated underline indicator */}
      <div
        className="absolute bottom-0 h-0.5 bg-t1 transition-[left,width] duration-fast ease-standard"
        style={{ left: indicator.left, width: indicator.width }}
      />
    </div>
  );
}
```

---

## 7. Badge

### Anatomy

```
[STATIC]  [FLEX]  [Q4_K_M]  [COURIER CLOUD]  [● ONLINE]
```

### Token Usage

| Variant   | Background              | Text Color         |
|-----------|-------------------------|--------------------|
| default   | `var(--gray-4)/0.15`    | `var(--t2)`        |
| primary   | `var(--brand)/0.10`     | `var(--brand)`     |
| success   | `var(--success)/0.10`   | `var(--success)`   |
| warning   | `var(--warning)/0.10`   | `var(--warning)`   |
| error     | `var(--error)/0.10`     | `var(--error)`     |

All variants share:
- `type-xs`, `uppercase`, `tracking-wide`
- `px-2 py-0.5` padding
- `rounded-sm` radius
- No border (bg-only)

### Interaction States

Badges are **non-interactive** by default. When used inside a clickable parent, they inherit the parent's hover/active state. No independent hover.

### Courier-Specific Notes

- Status badges include a `6px` dot before text: `<span className="size-1.5 rounded-full bg-current" />`.
- Badges wrap naturally — the parent container should have `flex-wrap` or `gap-1.5`.
- Never stack more than 4 badges in a single row — if more context is needed, use a tooltip or expand section.

### Code Pattern

```tsx
type BadgeVariant = "default" | "primary" | "success" | "warning" | "error";

interface BadgeProps {
  variant?: BadgeVariant;
  dot?: boolean;        // show status dot
  children: React.ReactNode;
  className?: string;
}

const variantClasses: Record<BadgeVariant, string> = {
  default: "bg-gray-4/15 text-t2",
  primary: "bg-brand/10 text-brand",
  success: "bg-success/10 text-success",
  warning: "bg-warning/10 text-warning",
  error:   "bg-error/10 text-error",
};

function Badge({ variant = "default", dot, children, className }: BadgeProps) {
  return (
    <span
      className={cn(
        "inline-flex items-center gap-1 px-2 py-0.5 rounded-sm",
        "type-xs uppercase tracking-wide",
        variantClasses[variant],
        className,
      )}
    >
      {dot && <span className="size-1.5 rounded-full bg-current shrink-0" />}
      {children}
    </span>
  );
}
```

---

## 8. Button

### Anatomy & Variants

| Variant      | Background         | Text       | Height | Radius       |
|--------------|--------------------|------------|--------|--------------|
| Primary      | `var(--brand)`     | `white`    | `44px` | `radius-md`  |
| Ghost        | `transparent`      | `var(--t2)`| `36px` | `radius-pill` |
| Destructive  | `var(--error)`     | `white`    | `44px` | `radius-md`  |
| Outline      | `transparent`      | `var(--t1)`| `36px` | `radius-md`  |

### Token Usage

| Element      | Token / Class                                     |
|--------------|---------------------------------------------------|
| Text         | `type-sm`, `font-medium`                          |
| Icon         | `size-4`, positioned with `gap-2`                 |
| Padding      | `px-4` for text buttons, `p-0` for icon-only      |
| Border       | Ghost/Outline: `1px solid var(--border-mid)`       |
| Focus ring   | `ring-2 ring-brand/30 ring-offset-2 ring-offset-background` |

### Interaction States

| State    | Visual Change                                            |
|----------|----------------------------------------------------------|
| Default  | Per variant specs above                                  |
| Hover    | `opacity: 0.88` (hover guard)                           |
| Active   | `scale(0.97)`, `duration-fast`                           |
| Disabled | `opacity: 0.5`, `pointer-events: none`, no transform    |
| Loading  | Spinner replaces icon/text, button stays same width      |

### Courier-Specific Notes

- All buttons use `cursor-pointer` explicitly (Tailwind v4 resets this).
- Icon-only buttons need `aria-label` — enforce with a required prop or lint rule.
- Ghost buttons in dark mode are preferred for secondary actions; avoid outline on dark.
- Destructive buttons require a confirmation step (dialog or double-click guard).
- Min-width `80px` for text buttons to prevent undersized tap targets.

### Code Pattern

```tsx
type ButtonVariant = "primary" | "ghost" | "destructive" | "outline";
type ButtonSize = "default" | "sm" | "icon" | "icon-sm";

interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: ButtonVariant;
  size?: ButtonSize;
  loading?: boolean;
  asChild?: boolean;
}

const variantStyles: Record<ButtonVariant, string> = {
  primary:     "bg-brand text-white h-11 rounded-md",
  ghost:       "bg-transparent text-t2 h-9 rounded-full border border-border-mid",
  destructive: "bg-error text-white h-11 rounded-md",
  outline:     "bg-transparent text-t1 h-9 rounded-md border border-border-mid",
};

const sizeStyles: Record<ButtonSize, string> = {
  default:  "px-4 min-w-[80px]",
  sm:       "px-3 h-8 min-w-[64px]",
  icon:     "size-11 p-0",
  "icon-sm":"size-8 p-0",
};

function Button({
  variant = "primary", size = "default", loading, disabled,
  children, className, ...props
}: ButtonProps) {
  return (
    <button
      disabled={disabled || loading}
      className={cn(
        "inline-flex items-center justify-center gap-2",
        "type-sm font-medium cursor-pointer select-none",
        "transition-[opacity,transform] duration-fast ease-standard",
        "hover-guard:hover:opacity-88",
        "active:scale-[0.97]",
        "disabled:opacity-50 disabled:pointer-events-none disabled:!transform-none",
        "focus-visible:ring-2 focus-visible:ring-brand/30",
        "focus-visible:ring-offset-2 focus-visible:ring-offset-background",
        variantStyles[variant],
        sizeStyles[size],
        className,
      )}
      {...props}
    >
      {loading ? <Loader2 className="size-4 animate-spin" /> : children}
    </button>
  );
}
```

---

## 9. Input

### Anatomy

```
┌──────────────────────────────────┐
│ 🔍  Search models...             │  ← optional icon + placeholder
└──────────────────────────────────┘
```

### Token Usage

| Element       | Token / Class                                    |
|---------------|--------------------------------------------------|
| Height        | `40px`                                           |
| Border        | `1px solid transparent` default                  |
| Radius        | `var(--radius-sm)`                               |
| Background    | `var(--bg-input)` — subtle filled approach        |
| Text          | `type-sm`, `var(--t1)`, `min-size: 16px`          |
| Placeholder   | `var(--t3)`                                       |
| Focus border  | `var(--brand)`                                    |
| Focus ring    | `var(--brand)/0.10`, `3px` spread                 |
| Error border  | `var(--error)`                                    |
| Error ring    | `var(--error)/0.10`, `3px` spread                 |
| Icon          | `size-4`, `var(--t3)`, positioned `left-3`        |
| Padding       | `px-3` (no icon) or `pl-9 pr-3` (with left icon) |

### Interaction States

| State    | Visual Change                                            |
|----------|----------------------------------------------------------|
| Default  | `bg-input`, `border-transparent`                         |
| Hover    | `border-color: var(--border-mid)` (hover guard)          |
| Focus    | `border-color: var(--brand)`, `ring: brand/10 3px`       |
| Error    | `border-color: var(--error)`, `ring: error/10 3px`       |
| Disabled | `opacity: 0.5`, `cursor: not-allowed`                    |

### Courier-Specific Notes

- **Minimum 16px font size** on inputs to prevent iOS Safari auto-zoom on focus.
- Use filled input style (subtle bg, no visible border) to reduce visual noise in dark UIs.
- Inputs inside forms use `FormField` wrapper from shadcn for label + error message integration.
- Search inputs include a `Loader2` spinner replacing the search icon during async search.
- Clearable inputs show an `X` button on the right when value is non-empty.

### Code Pattern

```tsx
interface InputProps extends React.InputHTMLAttributes<HTMLInputElement> {
  icon?: LucideIcon;
  error?: boolean;
}

const Input = forwardRef<HTMLInputElement, InputProps>(
  ({ icon: Icon, error, className, ...props }, ref) => {
    return (
      <div className="relative">
        {Icon && (
          <Icon className="absolute left-3 top-1/2 -translate-y-1/2 size-4 text-t3 pointer-events-none" />
        )}
        <input
          ref={ref}
          data-error={error || undefined}
          className={cn(
            "h-10 w-full rounded-sm bg-input",
            "border border-transparent",
            "type-sm text-t1 placeholder:text-t3",
            "min-text-[16px]",  // iOS zoom prevention
            Icon ? "pl-9 pr-3" : "px-3",
            // transitions
            "transition-[border-color,box-shadow] duration-fast ease-standard",
            // states
            "hover-guard:hover:border-border-mid",
            "focus:outline-none focus:border-brand focus:ring-3 focus:ring-brand/10",
            "data-[error]:border-error data-[error]:ring-3 data-[error]:ring-error/10",
            "disabled:opacity-50 disabled:cursor-not-allowed",
            className,
          )}
          {...props}
        />
      </div>
    );
  }
);
Input.displayName = "Input";
```

---

## 10. Modal / Dialog

### Anatomy

```
┌─── overlay (bg-overlay) ────────────────────────┐
│                                                  │
│   ┌─── surface ────────────────────────────┐     │
│   │ Dialog Title                     [✕]   │     │
│   │ ───────────────────────────────────     │     │
│   │                                        │     │
│   │ Content area                           │     │
│   │                                        │     │
│   │ ───────────────────────────────────     │     │
│   │              [Cancel] [Confirm]        │     │
│   └────────────────────────────────────────┘     │
│                                                  │
└──────────────────────────────────────────────────┘
```

### Token Usage

| Element       | Token / Class                                     |
|---------------|---------------------------------------------------|
| Overlay bg    | `var(--bg-overlay)` — `black` at `70%` alpha       |
| Surface bg    | `var(--bg-elevated)`                               |
| Surface border| `1px solid var(--border)`                          |
| Surface radius| `var(--radius-lg)`                                 |
| Surface shadow| `var(--shadow-lg)`                                 |
| Width         | `min(calc(100vw - 2rem), 640px)` — content-dependent |
| Small dialog  | `max-width: 400px`                                 |
| Large dialog  | `max-width: 640px`                                 |
| Title         | `type-lg`, `var(--t1)`, `font-heading`             |
| Close button  | Top-right, icon-only ghost button, `size-8`        |
| Footer        | `flex justify-end gap-2`, `border-top border-border`, `pt-4` |
| Padding       | `p-6`                                              |

### Interaction States

| State    | Visual Change                                              |
|----------|-------------------------------------------------------------|
| Enter    | Overlay: fade-in `duration-smooth` (300ms). Surface: fade-in + `translateY(4px)→0` |
| Exit     | Overlay: fade-out `duration-exit` (210ms). Surface: fade-out + `translateY(0)→4px`  |
| Idle     | Click outside or `Escape` closes. Focus trapped inside.     |

### Courier-Specific Notes

- Built on shadcn `Dialog` primitives (`DialogContent`, `DialogHeader`, etc.).
- Always include close button AND keyboard escape — both are required.
- Destructive modals (delete model, revoke key) use `Destructive` button for confirm.
- Long content: body area scrolls independently, header/footer stay fixed.
- Enter animation: `300ms` ease-out. Exit: `210ms` ease-in. Asymmetric is intentional — fast exit feels snappier.

### Code Pattern

```tsx
interface ModalProps {
  open: boolean;
  onOpenChange: (open: boolean) => void;
  title: string;
  description?: string;
  size?: "sm" | "md" | "lg";
  children: React.ReactNode;
  footer?: React.ReactNode;
}

const sizeClasses = {
  sm: "max-w-[400px]",
  md: "max-w-[520px]",
  lg: "max-w-[640px]",
};

function Modal({
  open, onOpenChange, title, description, size = "md", children, footer,
}: ModalProps) {
  return (
    <Dialog open={open} onOpenChange={onOpenChange}>
      <DialogOverlay
        className={cn(
          "fixed inset-0 z-50 bg-overlay",
          "data-[state=open]:animate-in data-[state=open]:fade-in duration-smooth",
          "data-[state=closed]:animate-out data-[state=closed]:fade-out duration-exit",
        )}
      />
      <DialogContent
        className={cn(
          "fixed left-1/2 top-1/2 z-50 -translate-x-1/2 -translate-y-1/2",
          "w-[calc(100vw-2rem)] p-6",
          "bg-elevated border border-border rounded-lg shadow-lg",
          sizeClasses[size],
          // enter
          "data-[state=open]:animate-in data-[state=open]:fade-in",
          "data-[state=open]:slide-in-from-bottom-1 duration-smooth",
          // exit
          "data-[state=closed]:animate-out data-[state=closed]:fade-out",
          "data-[state=closed]:slide-out-to-bottom-1 duration-exit",
        )}
      >
        {/* Header */}
        <DialogHeader>
          <DialogTitle className="type-lg font-heading text-t1">
            {title}
          </DialogTitle>
          {description && (
            <DialogDescription className="type-sm text-t2 mt-1">
              {description}
            </DialogDescription>
          )}
        </DialogHeader>

        {/* Close */}
        <DialogClose asChild>
          <Button
            variant="ghost"
            size="icon-sm"
            className="absolute top-4 right-4"
            aria-label="Close"
          >
            <X className="size-4" />
          </Button>
        </DialogClose>

        {/* Body */}
        <div className="mt-4 max-h-[60vh] overflow-y-auto">
          {children}
        </div>

        {/* Footer */}
        {footer && (
          <div className="flex justify-end gap-2 border-t border-border pt-4 mt-4">
            {footer}
          </div>
        )}
      </DialogContent>
    </Dialog>
  );
}
```

---

## 11. Tooltip

### Token Usage

| Element    | Token / Class                                     |
|------------|---------------------------------------------------|
| Background | `var(--gray-12)` — near-white on dark theme        |
| Text color | `var(--gray-1)` — inverted, near-black on dark     |
| Text       | `type-xs`                                          |
| Max width  | `240px`                                            |
| Radius     | `var(--radius-sm)`                                 |
| Padding    | `px-2 py-1`                                        |
| Arrow      | Same bg as tooltip, rotated `45deg` square          |
| Shadow     | `var(--shadow-sm)`                                 |
| Delay      | `300ms` before show, `0ms` on hide                 |
| Z-index    | `z-50`                                             |

### Interaction States

| State   | Visual Change                                          |
|---------|--------------------------------------------------------|
| Hidden  | `opacity: 0`, `scale(0.96)`, `pointer-events: none`    |
| Visible | `opacity: 1`, `scale(1)`, after `300ms` delay           |
| Exit    | `opacity: 0`, `duration-fast`                           |

### Courier-Specific Notes

- Use shadcn `Tooltip` with `TooltipProvider` at app root (sets `delayDuration={300}`).
- Inverted color scheme (light bg on dark UI) ensures maximum contrast.
- Keep tooltip text under 60 characters. For longer content, use a popover instead.
- Tooltips on disabled elements: wrap the trigger in a `<span>` so hover still fires.

### Code Pattern

```tsx
interface CourierTooltipProps {
  content: string;
  side?: "top" | "right" | "bottom" | "left";
  children: React.ReactNode;
}

function CourierTooltip({ content, side = "top", children }: CourierTooltipProps) {
  return (
    <Tooltip>
      <TooltipTrigger asChild>{children}</TooltipTrigger>
      <TooltipContent
        side={side}
        className={cn(
          "z-50 px-2 py-1 max-w-[240px]",
          "bg-gray-12 text-gray-1 type-xs rounded-sm shadow-sm",
          "animate-in fade-in zoom-in-96 duration-fast",
          "data-[state=closed]:animate-out data-[state=closed]:fade-out duration-fast",
        )}
      >
        {content}
      </TooltipContent>
    </Tooltip>
  );
}
```

---

## 12. Dropdown Menu

### Token Usage

| Element       | Token / Class                                    |
|---------------|--------------------------------------------------|
| Container bg  | `var(--bg-elevated)`                              |
| Border        | `1px solid var(--border)`                         |
| Shadow        | `var(--shadow-md)`                                |
| Radius        | `var(--radius-md)`                                |
| Item height   | `36px`                                            |
| Item text     | `type-sm`, `var(--t1)`                            |
| Item icon     | `size-4`, `var(--t2)`, `mr-2`                     |
| Item padding  | `px-2`                                            |
| Separator     | `1px solid var(--border)`, `my-1`                 |
| Destructive   | `var(--error)` text and icon                      |
| Min width     | `180px`                                           |

### Interaction States

| State    | Visual Change                                           |
|----------|---------------------------------------------------------|
| Item default  | `bg: transparent`, `text-t1`                       |
| Item hover    | `bg: var(--gray-4)/0.15` (hover guard)             |
| Item focus    | Same as hover (keyboard nav)                       |
| Item disabled | `opacity: 0.5`, `pointer-events: none`             |
| Destructive   | `text-error`, hover bg `var(--error)/0.10`         |
| Open          | `animate-in fade-in slide-in-from-top-1 duration-fast` |
| Close         | `animate-out fade-out duration-fast`               |

### Courier-Specific Notes

- Built on shadcn `DropdownMenu` primitives.
- Group related items between separators. Destructive items always in their own group at the bottom.
- Keyboard shortcuts shown right-aligned in `type-xs text-t3 font-mono`.
- Submenus are rare in Courier — prefer flat menus with grouping.

### Code Pattern

```tsx
interface MenuItem {
  label: string;
  icon?: LucideIcon;
  shortcut?: string;
  destructive?: boolean;
  disabled?: boolean;
  onClick: () => void;
}

interface MenuGroup {
  items: MenuItem[];
}

interface CourierDropdownProps {
  trigger: React.ReactNode;
  groups: MenuGroup[];
  align?: "start" | "center" | "end";
}

function CourierDropdown({ trigger, groups, align = "end" }: CourierDropdownProps) {
  return (
    <DropdownMenu>
      <DropdownMenuTrigger asChild>{trigger}</DropdownMenuTrigger>
      <DropdownMenuContent
        align={align}
        className={cn(
          "z-50 min-w-[180px] p-1",
          "bg-elevated border border-border rounded-md shadow-md",
          "animate-in fade-in slide-in-from-top-1 duration-fast",
          "data-[state=closed]:animate-out data-[state=closed]:fade-out duration-fast",
        )}
      >
        {groups.map((group, gi) => (
          <Fragment key={gi}>
            {gi > 0 && <DropdownMenuSeparator className="my-1 bg-border" />}
            {group.items.map((item) => (
              <DropdownMenuItem
                key={item.label}
                disabled={item.disabled}
                onClick={item.onClick}
                className={cn(
                  "flex items-center h-9 px-2 rounded-sm cursor-pointer",
                  "type-sm transition-colors duration-fast",
                  "hover-guard:hover:bg-gray-4/15",
                  "focus:bg-gray-4/15 focus:outline-none",
                  "disabled:opacity-50 disabled:pointer-events-none",
                  item.destructive
                    ? "text-error hover-guard:hover:bg-error/10"
                    : "text-t1",
                )}
              >
                {item.icon && (
                  <item.icon className={cn("size-4 mr-2", item.destructive ? "text-error" : "text-t2")} />
                )}
                <span className="flex-1">{item.label}</span>
                {item.shortcut && (
                  <span className="ml-4 type-xs text-t3 font-mono">{item.shortcut}</span>
                )}
              </DropdownMenuItem>
            ))}
          </Fragment>
        ))}
      </DropdownMenuContent>
    </DropdownMenu>
  );
}
```

---

## 13. API Config Display

### Anatomy

```
┌──────────────────────────────────────┐
│ Endpoint                             │
│ ┌──────────────────────────────────┐ │
│ │ http://localhost:8080/v1/chat... │ │  ← mono, truncated, copy button
│ └──────────────────────────────────┘ │
│                                      │
│ API Key                              │
│ ┌──────────────────────────────────┐ │
│ │ sk-••••••••••••••3f2a  [👁] [📋]│ │  ← masked, toggle, copy
│ └──────────────────────────────────┘ │
└──────────────────────────────────────┘
```

### Token Usage

| Element         | Token / Class                                    |
|-----------------|--------------------------------------------------|
| Container       | `bg-elevated`, `border`, `rounded-card`, `p-4`    |
| Field label     | `type-xs`, `uppercase`, `tracking-wide`, `var(--t3)`, `mb-1.5` |
| Value container | `bg-input`, `rounded-sm`, `h-10`, `px-3`          |
| Value text      | `font-mono`, `type-sm`, `var(--t1)`, `truncate`   |
| Masked text     | `font-mono`, `type-sm`, `var(--t3)`, `tracking-wider` |
| Action buttons  | Icon-only ghost, `size-7`, inside the value container |
| Gap between fields | `var(--space-4)` (16px)                        |

### Interaction States

| State    | Visual Change                                            |
|----------|----------------------------------------------------------|
| Default  | Value masked (API key), full (endpoint)                  |
| Revealed | Full value shown, eye icon toggles to "eye-off"          |
| Copied   | Button icon briefly changes to `Check`, resets after 2s  |
| Hover    | Value container: `border-color: var(--border-mid)`       |

### Courier-Specific Notes

- API keys are always masked by default — reveal requires explicit toggle.
- The mask format: first 3 chars + `••••••••••••` + last 4 chars.
- Copy button uses `navigator.clipboard.writeText()` with a success toast.
- Endpoint URLs can be very long — always truncate with ellipsis, full value on hover tooltip.
- Never log or persist revealed API keys in any state management.

### Code Pattern

```tsx
interface ConfigField {
  label: string;
  value: string;
  masked?: boolean;   // show mask by default
  copyable?: boolean;
}

interface ApiConfigDisplayProps {
  fields: ConfigField[];
}

function ApiConfigDisplay({ fields }: ApiConfigDisplayProps) {
  return (
    <div className="flex flex-col gap-4 bg-elevated border border-border rounded-card p-4">
      {fields.map((field) => (
        <ConfigFieldRow key={field.label} {...field} />
      ))}
    </div>
  );
}

function ConfigFieldRow({ label, value, masked = false, copyable = true }: ConfigField) {
  const [revealed, setRevealed] = useState(false);
  const [copied, setCopied] = useState(false);

  const displayValue = masked && !revealed
    ? `${value.slice(0, 3)}${"•".repeat(12)}${value.slice(-4)}`
    : value;

  async function handleCopy() {
    await navigator.clipboard.writeText(value);
    setCopied(true);
    setTimeout(() => setCopied(false), 2000);
  }

  return (
    <div>
      <label className="type-xs uppercase tracking-wide text-t3 mb-1.5 block">
        {label}
      </label>
      <div
        className={cn(
          "flex items-center gap-1 h-10 px-3",
          "bg-input rounded-sm",
          "transition-colors duration-fast ease-standard",
          "hover-guard:hover:border hover-guard:hover:border-border-mid",
        )}
      >
        <span
          className={cn(
            "flex-1 font-mono type-sm truncate",
            masked && !revealed ? "text-t3 tracking-wider" : "text-t1",
          )}
        >
          {displayValue}
        </span>

        <div className="flex items-center gap-0.5 shrink-0">
          {masked && (
            <Button
              variant="ghost"
              size="icon-sm"
              onClick={() => setRevealed(!revealed)}
              aria-label={revealed ? "Hide" : "Reveal"}
              className="size-7"
            >
              {revealed ? <EyeOff className="size-3.5" /> : <Eye className="size-3.5" />}
            </Button>
          )}
          {copyable && (
            <Button
              variant="ghost"
              size="icon-sm"
              onClick={handleCopy}
              aria-label="Copy"
              className="size-7"
            >
              {copied ? <Check className="size-3.5 text-success" /> : <Copy className="size-3.5" />}
            </Button>
          )}
        </div>
      </div>
    </div>
  );
}
```

---

## 14. Status Indicator

### Anatomy

```
● Online    ◐ Starting    ○ Offline    ⚠ Degraded
```

### Token Usage

| Status    | Color              | Animation                        |
|-----------|--------------------|----------------------------------|
| online    | `var(--success)`   | None (static)                    |
| offline   | `var(--error)`     | None (static)                    |
| degraded  | `var(--warning)`   | None (static)                    |
| starting  | `var(--brand)`     | Pulse (`animate-pulse`)          |
| deploying | `var(--brand)`     | Pulse (`animate-pulse`)          |
| stopping  | `var(--warning)`   | Pulse (`animate-pulse`)          |

Dot size: `6px` (`size-1.5`), perfectly round.

### Data-Attribute Styling

```css
.status-dot { width: 6px; height: 6px; border-radius: 50%; }

.status-dot[data-status="online"]    { background: var(--success); }
.status-dot[data-status="offline"]   { background: var(--error); }
.status-dot[data-status="degraded"]  { background: var(--warning); }
.status-dot[data-status="starting"],
.status-dot[data-status="deploying"] { background: var(--brand); animation: pulse 2s ease-in-out infinite; }
.status-dot[data-status="stopping"]  { background: var(--warning); animation: pulse 2s ease-in-out infinite; }

@keyframes pulse {
  0%, 100% { opacity: 1; }
  50%      { opacity: 0.4; }
}
```

### Courier-Specific Notes

- Pulse animation is `2s` period — slow enough to not be distracting, fast enough to convey "in progress."
- Status indicators appear in: Model Cards, Data Tables, Sidebar nav (system health), and the top bar.
- Always pair the dot with a text label for accessibility. Dot alone is insufficient.
- Transitional states (starting, deploying, stopping) imply the dot will change — poll or subscribe for updates.

### Code Pattern

```tsx
type Status = "online" | "offline" | "degraded" | "starting" | "deploying" | "stopping";

interface StatusIndicatorProps {
  status: Status;
  label?: string;      // text label next to dot
  showLabel?: boolean;
  className?: string;
}

const statusLabels: Record<Status, string> = {
  online:    "Online",
  offline:   "Offline",
  degraded:  "Degraded",
  starting:  "Starting",
  deploying: "Deploying",
  stopping:  "Stopping",
};

const statusColors: Record<Status, string> = {
  online:    "bg-success",
  offline:   "bg-error",
  degraded:  "bg-warning",
  starting:  "bg-brand animate-pulse-slow",
  deploying: "bg-brand animate-pulse-slow",
  stopping:  "bg-warning animate-pulse-slow",
};

function StatusIndicator({ status, label, showLabel = true, className }: StatusIndicatorProps) {
  const displayLabel = label ?? statusLabels[status];

  return (
    <span className={cn("inline-flex items-center gap-1.5", className)}>
      <span
        data-status={status}
        className={cn("size-1.5 rounded-full shrink-0", statusColors[status])}
        aria-hidden="true"
      />
      {showLabel && (
        <span className="type-sm text-t1">{displayLabel}</span>
      )}
      <span className="sr-only">{displayLabel}</span>
    </span>
  );
}
```

---

## 15. Capacity Meter

### Anatomy

```
Memory Usage
[████████████░░░░░░░░] 62% (4.2 / 6.8 GB)
                                   ▲ threshold marker
```

### Token Usage

| Element         | Token / Class                                     |
|-----------------|---------------------------------------------------|
| Label           | `type-xs`, `var(--t2)`, `uppercase`, `tracking-wide` |
| Value text      | `type-sm`, `var(--t1)`, `tabular-nums`             |
| Track bg        | `var(--gray-3)` or `var(--bg-input)`               |
| Track height    | `8px`                                              |
| Track radius    | `var(--radius-pill)` (fully round)                  |
| Fill (safe)     | `var(--brand)` (0-69%)                              |
| Fill (warning)  | `var(--warning)` (70-89%)                           |
| Fill (critical) | `var(--error)` (90%+)                               |
| Threshold marker| `2px wide`, `var(--t3)`, positioned at threshold %  |
| Container gap   | `var(--space-1)` between label row and bar          |

### Interaction States

| State    | Visual Change                                           |
|----------|---------------------------------------------------------|
| Default  | Fill color based on percentage tier                     |
| Hover    | Tooltip showing exact values (hover guard)              |
| Warning  | Fill shifts to `var(--warning)`, optional label emphasis |
| Critical | Fill shifts to `var(--error)`, optional pulse on fill    |
| Empty    | Empty track, `0%` label                                 |

### Data-Attribute Styling

```css
.capacity-fill[data-tier="safe"]     { background: var(--brand); }
.capacity-fill[data-tier="warning"]  { background: var(--warning); }
.capacity-fill[data-tier="critical"] { background: var(--error); animation: pulse 2s ease-in-out infinite; }
```

### Courier-Specific Notes

- Tier thresholds default to 70% (warning) and 90% (critical), but are configurable per instance.
- Capacity meters are used for: GPU memory, VRAM, CPU utilization, disk space, context window usage.
- The threshold marker (vertical line on the track) shows where the warning tier begins.
- Transition the fill width with `transition: width var(--duration-smooth) var(--ease-standard)` for live-updating values.
- When value exceeds capacity (over 100%), clamp fill at 100% and show value in `var(--error)` with exclamation icon.

### Code Pattern

```tsx
interface CapacityMeterProps {
  label: string;
  value: number;        // current value (e.g. 4.2)
  max: number;          // max capacity (e.g. 6.8)
  unit: string;         // e.g. "GB", "MB", "%"
  warningAt?: number;   // percentage threshold (default 70)
  criticalAt?: number;  // percentage threshold (default 90)
  showThreshold?: boolean;
}

function CapacityMeter({
  label, value, max, unit,
  warningAt = 70, criticalAt = 90,
  showThreshold = true,
}: CapacityMeterProps) {
  const percentage = Math.min(Math.round((value / max) * 100), 100);
  const tier: "safe" | "warning" | "critical" =
    percentage >= criticalAt ? "critical" :
    percentage >= warningAt  ? "warning"  : "safe";

  return (
    <div className="flex flex-col gap-1">
      {/* Header row: label + values */}
      <div className="flex items-baseline justify-between">
        <span className="type-xs uppercase tracking-wide text-t2">{label}</span>
        <span className="type-sm text-t1 tabular-nums">
          {percentage}%
          <span className="text-t3 ml-1">
            ({value} / {max} {unit})
          </span>
        </span>
      </div>

      {/* Track */}
      <div className="relative h-2 w-full bg-input rounded-full overflow-hidden">
        {/* Fill */}
        <div
          data-tier={tier}
          className={cn(
            "capacity-fill h-full rounded-full",
            "transition-[width,background-color] duration-smooth ease-standard",
          )}
          style={{ width: `${percentage}%` }}
          role="meter"
          aria-valuenow={value}
          aria-valuemin={0}
          aria-valuemax={max}
          aria-label={`${label}: ${percentage}%`}
        />

        {/* Threshold marker */}
        {showThreshold && (
          <div
            className="absolute top-0 bottom-0 w-0.5 bg-t3/50"
            style={{ left: `${warningAt}%` }}
            aria-hidden="true"
          />
        )}
      </div>
    </div>
  );
}
```

---

## Quick Reference: Token Cheat Sheet

For rapid implementation, here are the most-used token mappings across all components:

| Purpose           | Token                        | Usage                              |
|--------------------|-----------------------------|------------------------------------|
| Primary text       | `var(--t1)` / `text-t1`     | Values, names, active labels       |
| Secondary text     | `var(--t2)` / `text-t2`     | Labels, descriptions, nav items    |
| Tertiary text      | `var(--t3)` / `text-t3`     | Placeholders, hints, axis labels   |
| Elevated surface   | `var(--bg-elevated)`         | Cards, dialogs, dropdowns          |
| Input surface      | `var(--bg-input)`            | Inputs, tracks, code blocks        |
| Overlay            | `var(--bg-overlay)`          | Modal backdrops                    |
| Default border     | `var(--border)`              | Card edges, dividers               |
| Mid border         | `var(--border-mid)`          | Hover borders                      |
| Brand color        | `var(--brand)`               | Primary buttons, chart lines, active indicators |
| Success signal     | `var(--success)`             | Online status, positive delta      |
| Warning signal     | `var(--warning)`             | Degraded status, threshold warning |
| Error signal       | `var(--error)`               | Offline status, negative delta, destructive |
| Fast transition    | `var(--duration-fast)`       | Hover, color changes (~150ms)      |
| Smooth transition  | `var(--duration-smooth)`     | Layout shifts, modals (~300ms)     |
| Exit transition    | `var(--duration-exit)`       | Dialog close, removal (~210ms)     |
| Standard easing    | `var(--ease-standard)`       | Default easing for all transitions |
| Card radius        | `var(--radius-card)`         | Cards, containers                  |
| Small radius       | `var(--radius-sm)`           | Inputs, badges                     |
| Medium radius      | `var(--radius-md)`           | Buttons, dropdowns                 |
| Large radius       | `var(--radius-lg)`           | Dialogs                            |
| Pill radius        | `var(--radius-pill)`         | Ghost buttons, progress tracks     |

---

## Anti-Patterns

Patterns that violate the Courier design system. If you see these in a PR, flag them.

| Anti-Pattern | Correct Approach |
|---|---|
| Raw Tailwind colors (`bg-zinc-800`) | Semantic token (`bg-elevated`) |
| Inline transition values (`transition: all 0.2s ease`) | Token classes (`transition-colors duration-fast ease-standard`) |
| Hover without `@media (hover: hover)` guard | Wrap in hover guard |
| `className` ternary for state (`isActive ? "text-white" : "text-gray"`) | `data-active` attribute + CSS selector |
| Animation on tab content switch | Only animate the indicator |
| Numeric values without `tabular-nums` | Always `tabular-nums` for live data |
| Input font-size under 16px | Minimum 16px (iOS zoom prevention) |
| Modal without keyboard escape handler | Use shadcn Dialog (handles it) |
| Status dot without text label | Always pair dot + label for a11y |
| Hardcoded shadow values | Use `shadow-sm`, `shadow-md`, `shadow-lg` tokens |
