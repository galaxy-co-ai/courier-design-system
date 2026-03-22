# Step 8: Page Patterns — Reference Implementations

## Purpose

Build reference page implementations that compose all Courier design system components into real, functioning layouts. These pages serve as templates Jackson can clone and modify for new views. Every page uses mock data, imports only from the component library built in Steps 4-6, and demonstrates how the design system comes together in practice.

## Dependencies

Steps 0-7 must be complete:
- Step 0: Brand audit
- Step 1: Token foundation (globals.css)
- Step 2: Core primitives (Button, Badge, Input, etc.)
- Step 3: Typography system
- Step 4: Core components (cards, dialogs, etc.)
- Step 5: Data components (charts, tables, metrics, ModelCard)
- Step 6: Navigation components (sidebar, tabs, app shell)
- Step 7: Motion & interaction system (all transitions standardized)

All components exist and use motion tokens. This step composes them — zero new primitives should be created.

## Quality Context

Before doing ANY work, read these files:

```
skills/ui-engineering/SKILL.md
constitution/ENGINEERING-CONSTITUTION.md
skills/ui-engineering/references/component-patterns.md
```

Athlete Era interaction patterns are the benchmark for page-level composition. Study how components are composed into views — density, spacing rhythm, information hierarchy.

## Instructions

### Phase 1: Full Component Inventory

Read every file that will be imported:

1. `src/app/globals.css` — all tokens
2. Every component under `src/components/` and `src/design-system/`
3. The app shell / layout files (`src/app/**/layout.tsx`)
4. `skills/ui-engineering/references/component-patterns.md`

Catalog every component available for composition. You must know exactly what exists before building pages. Do not create new primitives — if a page needs something that doesn't exist, compose it from existing pieces.

### Phase 2: Mock Data

Create a mock data file at `src/lib/mock-data.ts` (or add to existing) with realistic Courier data:

```typescript
// Model data — use real model names, realistic sizes and performance numbers
export const mockModels = [
  {
    id: "llama-3.2-3b",
    name: "Llama 3.2 3B",
    provider: "Meta",
    parameterCount: "3B",
    quantization: "Q4_K_M",
    status: "running" as const,
    memoryUsage: 2.1,      // GB
    avgLatency: 12.4,       // ms/token
    totalInferences: 14_829,
    lastActive: new Date("2026-03-21T14:32:00"),
    downloadSize: "1.8 GB",
  },
  {
    id: "mistral-7b-v0.3",
    name: "Mistral 7B v0.3",
    provider: "Mistral AI",
    parameterCount: "7B",
    quantization: "Q5_K_M",
    status: "running" as const,
    memoryUsage: 4.8,
    avgLatency: 28.7,
    totalInferences: 8_412,
    lastActive: new Date("2026-03-21T14:28:00"),
    downloadSize: "4.2 GB",
  },
  {
    id: "phi-3-mini",
    name: "Phi-3 Mini",
    provider: "Microsoft",
    parameterCount: "3.8B",
    quantization: "Q4_0",
    status: "stopped" as const,
    memoryUsage: 0,
    avgLatency: 0,
    totalInferences: 3_201,
    lastActive: new Date("2026-03-20T09:15:00"),
    downloadSize: "2.0 GB",
  },
  {
    id: "gemma-2-2b",
    name: "Gemma 2 2B",
    provider: "Google",
    parameterCount: "2B",
    quantization: "Q8_0",
    status: "running" as const,
    memoryUsage: 2.8,
    avgLatency: 8.1,
    totalInferences: 22_507,
    lastActive: new Date("2026-03-21T14:35:00"),
    downloadSize: "2.3 GB",
  },
  {
    id: "codellama-13b",
    name: "Code Llama 13B",
    provider: "Meta",
    parameterCount: "13B",
    quantization: "Q4_K_S",
    status: "stopped" as const,
    memoryUsage: 0,
    avgLatency: 0,
    totalInferences: 1_044,
    lastActive: new Date("2026-03-18T16:42:00"),
    downloadSize: "7.1 GB",
  },
];

// Metric snapshots
export const mockMetrics = {
  totalModels: 5,
  activeModels: 3,
  avgLatency: 16.4,        // ms/token across active models
  totalMemory: 9.7,         // GB used
  availableMemory: 22.3,    // GB remaining (32GB M2 Max)
  totalInferences: 49_993,
  uptimeHours: 127,
};

// Time-series data for charts (last 24h, hourly)
export const mockThroughputData = Array.from({ length: 24 }, (_, i) => ({
  hour: new Date(Date.now() - (23 - i) * 3600_000).toISOString(),
  inferences: Math.floor(80 + Math.random() * 120),
  tokensPerSecond: Math.floor(45 + Math.random() * 35),
}));

// Activity feed
export const mockActivity = [
  { id: "1", type: "model_loaded" as const, model: "Llama 3.2 3B", timestamp: new Date("2026-03-21T14:32:00"), detail: "Loaded in 3.2s, 2.1 GB allocated" },
  { id: "2", type: "inference" as const, model: "Gemma 2 2B", timestamp: new Date("2026-03-21T14:28:00"), detail: "512 tokens generated, 8.1 ms/tok" },
  { id: "3", type: "model_stopped" as const, model: "Phi-3 Mini", timestamp: new Date("2026-03-20T09:15:00"), detail: "Stopped by user, 2.0 GB freed" },
  { id: "4", type: "error" as const, model: "Code Llama 13B", timestamp: new Date("2026-03-20T08:30:00"), detail: "OOM — exceeded 16 GB limit, model unloaded" },
  { id: "5", type: "api_key_created" as const, model: null, timestamp: new Date("2026-03-19T11:00:00"), detail: "New API key created: ck_...a4f2" },
];

// API keys for settings page
export const mockApiKeys = [
  { id: "1", name: "Development", prefix: "ck_dev_", lastFour: "a4f2", created: new Date("2026-03-01"), lastUsed: new Date("2026-03-21T14:00:00") },
  { id: "2", name: "Production", prefix: "ck_prod_", lastFour: "9e1b", created: new Date("2026-02-15"), lastUsed: new Date("2026-03-21T13:45:00") },
  { id: "3", name: "CI/CD Pipeline", prefix: "ck_ci_", lastFour: "3d7c", created: new Date("2026-03-10"), lastUsed: new Date("2026-03-20T22:00:00") },
];
```

Adjust field names and types to match existing component props. The data must be realistic — real model names, plausible metrics, believable timestamps.

### Phase 3: Build Reference Pages

Build each page as a proper Next.js route component. Every page must:

- Use the App Shell layout (sidebar + top bar from Step 6)
- Be fully responsive (test at xl: 1280px, md: 768px, sm: 640px breakpoints)
- Have loading states using skeleton compositions from Step 7
- Import only from the existing component library
- Use mock data from `src/lib/mock-data.ts`
- Follow Courier's dark-first theme with cyan/teal primary, amber/orange secondary

#### Page A: Dashboard / Home

**Route:** `src/app/(dashboard)/page.tsx` (or the appropriate route in Courier's structure)

**Layout:**
1. **Metric cards row** — 4-up grid (responsive: 4 cols → 2 cols → 1 col)
   - Total Models (count + "3 active" subtext)
   - Active Inferences (live count)
   - Avg Latency (ms/token with trend indicator)
   - Memory Usage (X.X / 32 GB with progress bar or visual)

2. **Throughput chart** — full width
   - AreaChart inside ChartContainer component
   - TimeRangeToggle above chart (24h | 7d | 30d)
   - Y-axis: inferences/hour, X-axis: time
   - Uses brand cyan/teal for the area fill

3. **Active models section** — below chart
   - Section header: "Active Models" with count badge
   - ModelCard grid (responsive: 3 cols → 2 → 1)
   - Each card shows: model name, provider, status, latency, memory
   - Only shows running models

4. **Recent activity feed** — compact list at bottom or sidebar
   - Each item: icon (by type), description, relative timestamp
   - Type-colored indicators (green for load, amber for inference, red for error)
   - "View all" link at bottom

**Loading state:** Create a `DashboardSkeleton` component that mirrors the layout with skeleton elements.

#### Page B: Model Workbench

**Route:** `src/app/(dashboard)/workbench/page.tsx`

**Layout:**
1. **PageHeader** — "Model Workbench" title + "Load Model" primary button (right-aligned)

2. **Filter bar** — below header
   - TabBar: All | Running | Stopped | Library
   - Search input (right side, with search icon)
   - Active tab filters the grid below

3. **ModelCard grid** — filtered by active tab
   - All: shows every model
   - Running: only status === "running"
   - Stopped: only status === "stopped"
   - Library: shows available-to-download models (use additional mock data)
   - Responsive grid: 3 → 2 → 1 columns

4. **Empty state** — when a filter returns no results
   - Uses the EmptyState pattern (see Page E below)
   - Different messaging per tab ("No stopped models", "All models are loaded", etc.)

**Loading state:** Skeleton grid of ModelCard-shaped rectangles.

#### Page C: Analytics

**Route:** `src/app/(dashboard)/analytics/page.tsx`

**Layout:**
1. **Header row** — "Analytics" title + TimeRangeToggle (24h | 7d | 30d)

2. **Metric cards row** — 4-up (same responsive pattern as Dashboard)
   - Total Inferences (period total)
   - Avg Latency (period average)
   - Error Rate (percentage with red/green indicator)
   - Uptime (hours or percentage)

3. **Charts section** — 2-column grid on xl, stacked on md
   - **Throughput chart** — AreaChart, inferences over time
   - **Latency chart** — LineChart, avg latency over time with per-model lines
   - **Error rate chart** — BarChart, errors by hour/day

4. **Per-model breakdown** — DataTable at bottom
   - Columns: Model, Inferences, Avg Latency, P95 Latency, Error Rate, Memory Peak
   - Sortable columns
   - Row click could navigate to model detail (wire as link, destination can 404)

**Loading state:** Skeleton with chart-shaped rectangles and table rows.

#### Page D: Settings

**Route:** `src/app/(dashboard)/settings/page.tsx`

**Layout — single-column, max-width constrained (65ch or 720px):**

1. **API Keys section**
   - Section header: "API Keys" with "Create Key" button
   - List of keys, each showing: name, prefix + masked value (`ck_dev_****a4f2`), created date, last used
   - Each key has: Copy button, Reveal/Hide toggle, Delete button (with confirmation)
   - Reveal shows the full key (mock: `ck_dev_abc123...full_key_a4f2`)

2. **Separator**

3. **Model Defaults section**
   - Default quantization (select/dropdown: Q4_0, Q4_K_M, Q5_K_M, Q8_0)
   - Default context length (number input, default 4096)
   - Auto-unload idle models toggle (with "after X minutes" input)
   - GPU layers (number input or "auto" toggle)

4. **Separator**

5. **Appearance section**
   - Theme toggle (Dark / Light / System) — use radio group or segmented control
   - Sidebar collapsed by default toggle
   - Show model memory in sidebar toggle

6. **Separator**

7. **About section**
   - Courier version (mock: v0.4.2)
   - Runtime info (Apple Silicon, Metal backend, etc.)
   - Links: GitHub, Documentation, Report Issue
   - "Check for Updates" button

**Loading state:** Skeleton sections matching the layout.

#### Page E: Empty State Pattern

**Location:** `src/components/empty-state.tsx` (reusable component)

This is a shared component, not a page. Used by Workbench, Analytics, and anywhere else content might be absent.

**Props:**
```typescript
interface EmptyStateProps {
  icon: LucideIcon;          // Lucide icon component
  title: string;             // e.g., "No models loaded"
  description: string;       // e.g., "Load a model from the library to get started"
  action?: {
    label: string;           // e.g., "Browse Library"
    onClick: () => void;
    variant?: "default" | "outline";
  };
  className?: string;
}
```

**Styling:**
- Icon: 48px, `--t3` color (muted)
- Title: `type-lg` weight, `--t1` color
- Description: `type-md`, `--t2` color, max-width 45ch, centered
- Action button: primary variant, below description with 16px gap
- Entire component: centered vertically and horizontally in its container, with padding

### Phase 4: Responsive Verification

For each page, verify the layout works at three breakpoints:

- **xl (1280px+):** Full layout — 4-up metric grids, multi-column charts, spacious sidebar
- **md (768px):** Reduced columns — 2-up metric grids, stacked charts, collapsed sidebar available
- **sm (640px):** Single column — everything stacked, full-width cards, hamburger menu for nav

Use responsive Tailwind classes: `grid-cols-1 md:grid-cols-2 xl:grid-cols-4` etc.

### Phase 5: Documentation

Create `src/design-system/docs/page-patterns.md` with:

1. **Composition Strategy** — how pages are built from components:
   - Pages import components, never define new styles
   - Layout uses CSS Grid for page-level structure
   - Spacing between sections uses consistent gap tokens
   - Every page follows: Header → Controls → Content → Footer pattern

2. **Page Inventory** — table of all reference pages:
   | Page | Route | Key Components Used | Breakpoints Tested |
   |------|-------|--------------------|--------------------|

3. **How to Create a New Page:**
   - Clone the closest reference page
   - Swap components and mock data
   - Verify responsive behavior at 3 breakpoints
   - Add loading skeleton
   - Add empty state for zero-data conditions

4. **Layout Patterns:**
   - Metric row: 4-up grid pattern
   - Chart + controls: TimeRangeToggle above ChartContainer
   - Data table: full-width with sort controls
   - Settings: single-column, section-separated
   - Grid of cards: responsive column pattern

5. **Mock Data Strategy:**
   - All mock data lives in `src/lib/mock-data.ts`
   - Use realistic values (real model names, plausible metrics)
   - Mock data types should match eventual API response types
   - Easy to swap for real data — just replace the import

## Output Checklist

When complete, verify:

- [ ] `src/lib/mock-data.ts` exists with realistic Courier data
- [ ] Dashboard page renders with metric cards, chart, model grid, activity feed
- [ ] Workbench page renders with tabs, search, filtered model grid, empty states
- [ ] Analytics page renders with time range toggle, charts, data table
- [ ] Settings page renders with API keys (mask/reveal/copy), toggles, sections
- [ ] `src/components/empty-state.tsx` exists and is used by Workbench (at minimum)
- [ ] Every page uses the App Shell layout
- [ ] Every page is responsive at xl / md / sm breakpoints
- [ ] Every page has a loading skeleton state
- [ ] No new primitive components were created — only compositions of existing ones
- [ ] All imports reference the component library from Steps 4-6
- [ ] `src/design-system/docs/page-patterns.md` exists with composition documentation

## Definition of Done

Jackson can open each reference page and see a complete, functioning dashboard UI populated with realistic mock data. Every component works in context — cards display model info, charts render data, tables sort, tabs filter. The pages demonstrate how the design system composes into real product views. A developer can clone any reference page and modify it for a new view in under 30 minutes.
