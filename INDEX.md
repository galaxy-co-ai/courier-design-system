# Courier Design System — File Index

> Complete map of every file in this repo. 28 files across 8 directories.

---

## Quick Start

1. **Clone** this repo
2. **Give `BOOTSTRAP.md` to Claude Code** — auto-wires everything into your project
3. **Read** `product-brief/COURIER-PRODUCT-BRIEF.md`
4. **Run** design system prompts `00` through `09` in order

---

## Root

| File | Purpose |
|------|---------|
| `README.md` | Overview, visual preview, and usage guide |
| `INDEX.md` | This file — complete map of everything |
| `CLAUDE.md` | Claude Code project instructions — copy to your Courier project root |
| `BOOTSTRAP.md` | One-step setup prompt — give this to Claude Code to auto-install |
| `.gitignore` | Standard Node/Next.js ignores |

## `research/`

Source material. Reference only — read these if you're curious about the methodology.

| File | What It Contains |
|------|------------------|
| `courier-profile.md` | Product profile — stack, fonts, colors, audience, architecture, competitive context |
| `courier-mcp-data.md` | MCP server capabilities and connection notes |
| `constitution-audit.md` | Transferable engineering patterns extracted and adapted for Courier |
| `design-system-audit.md` | Athlete Era + Results Roofing design system audit mapped to Courier |

## `constitution/`

Engineering quality standards and decision framework.

| File | What It Contains |
|------|------------------|
| `ENGINEERING-CONSTITUTION.md` | Design philosophy, code standards, quality gates, decision framework, anti-patterns |
| `QUICK-REFERENCE.md` | One-screen cheat sheet of key rules and token names |

## `skills/ui-engineering/`

Claude Code skill package. Triggers on any frontend, UI, component, layout, or styling work.

| File | What It Contains |
|------|------------------|
| `SKILL.md` | Skill definition — triggers, design language, component rules, quality bar |
| `references/design-tokens.md` | Token architecture — OKLCH colors, spacing, typography, borders, motion, shadcn bridge |
| `references/component-patterns.md` | 15 component patterns — ASCII diagrams, TypeScript interfaces, interaction states |
| `references/quality-checklist.md` | Pre-commit audit — visual, interaction, accessibility, code, performance checks |

## `product-brief/`

Product strategy based on real user interview.

| File | What It Contains |
|------|------------------|
| `COURIER-PRODUCT-BRIEF.md` | Power user research, competitive landscape, proposed IA, agent-first vision, design patterns |

## `design-system/prompts/`

10 executable prompts. Run in order — each depends on the previous step.

| Step | File | What It Builds |
|:----:|------|----------------|
| 0 | `00-brand-audit.prompt.md` | Extracts every visual value in the codebase |
| 1 | `01-color-system.prompt.md` | OKLCH color tokens, 12-step gray scale, accent system, shadcn bridge |
| 2 | `02-typography.prompt.md` | Compound type scale, font stacks, numeric display features |
| 3 | `03-spacing-layout.prompt.md` | 4px spacing scale, layout tokens, z-index, App Shell component |
| 4 | `04-components-core.prompt.md` | 10 atoms — Button, Badge, Input, Card, Dialog, Tooltip, Dropdown, Switch, Skeleton, Separator |
| 5 | `05-components-data.prompt.md` | 7 data components — MetricCard, ModelCard, DataTable, ChartContainer, AreaChart, TimeRangeToggle, CapacityMeter |
| 6 | `06-components-nav.prompt.md` | 5 nav components — Sidebar, TabBar, Breadcrumbs, PageHeader, CommandPalette |
| 7 | `07-motion-interaction.prompt.md` | Motion tokens, hover guards, focus rings, skeleton loading, theme-switch blocker |
| 8 | `08-patterns-pages.prompt.md` | 4 reference pages — Dashboard, Workbench, Analytics, Settings + EmptyState |
| 9 | `09-documentation.prompt.md` | Living docs — INDEX, token reference, quick reference, contributing guide |

## `designs/`

Dashboard preview for README hero image.

| File | What It Shows |
|------|---------------|
| `courier-dashboard-preview.png` | Dashboard concept — sidebar, metric cards, model cards, chart area |

## `docs/specs/`

Design specifications. These are the real deliverables — detailed enough for Claude Code to build from.

| File | What It Covers |
|------|---------------|
| `2026-03-22-training-page-design.md` | Complete Training page spec — three training modes (Teach/Recipe/Onboard), territory world map, campaign roadmaps, guardrails system, workbench progression, gamification scoping |
