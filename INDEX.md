# Courier Design System — Index

## Quick Start (TL;DR)

1. Read the product brief (`product-brief/COURIER-PRODUCT-BRIEF.md`) — it will change what you build next
2. Copy `CLAUDE.md` into your Courier project root
3. Copy `skills/` into your project (or reference from here)
4. Run the 10 design system prompts in order (`design-system/prompts/00-09`)

## File Map

### Root

| File | Purpose |
|------|---------|
| `CLAUDE.md` | Claude Code project instructions — copy to your Courier project root |
| `INDEX.md` | This file — complete map of everything in the repo |
| `README.md` | What this is and how to use it |
| `.gitignore` | Standard ignores |

### research/

Source material gathered during the design system build. Reference only — you don't need to read these unless you're curious about the process.

| File | Purpose |
|------|---------|
| `courier-profile.md` | Canonical product profile — stack, fonts, color identity, audience, architecture |
| `courier-mcp-data.md` | Raw data captured from Courier's MCP server (models, endpoints, config) |
| `constitution-audit.md` | Patterns extracted from a battle-tested engineering constitution, adapted for Courier |
| `design-system-audit.md` | Cross-project design system audit (Athlete Era + Results Roofing) mapped to Courier's needs |

### constitution/

Quality standards and decision framework for engineering and design.

| File | Purpose |
|------|---------|
| `ENGINEERING-CONSTITUTION.md` | Full engineering constitution — design philosophy, code standards, anti-patterns, decision framework |
| `QUICK-REFERENCE.md` | One-screen cheat sheet of the constitution's key rules |

### skills/ui-engineering/

Claude Code skill package. Triggers automatically on any frontend, UI, component, layout, or styling work.

| File | Purpose |
|------|---------|
| `SKILL.md` | Skill definition — triggers, design language, component rules, quality bar |
| `references/design-tokens.md` | Token architecture reference — colors, spacing, typography, borders, motion |
| `references/component-patterns.md` | Component API patterns — props, composition, accessibility, data display |
| `references/quality-checklist.md` | Pre-commit UI quality audit — run before shipping any visual change |

### product-brief/

Product strategy based on real user research.

| File | Purpose |
|------|---------|
| `COURIER-PRODUCT-BRIEF.md` | Product design brief — based on a real power user interview. Covers what Courier does well, what's missing, and where the product should go next |

### design-system/prompts/

10 executable prompts that build Courier's design system from scratch. Run them in order — each step depends on the one before it.

| # | File | What It Does |
|---|------|-------------|
| 0 | `00-brand-audit.prompt.md` | Scan the codebase and extract every visual value currently in use |
| 1 | `01-color-system.prompt.md` | Build a rationalized OKLCH color token system from the brand audit |
| 2 | `02-typography.prompt.md` | Define the complete type scale, font stacks, and numeric display features |
| 3 | `03-spacing-layout.prompt.md` | Define spacing scale, layout tokens, z-index system, and build the App Shell |
| 4 | `04-components-core.prompt.md` | Build the 10 foundational UI atoms (buttons, inputs, badges, etc.) |
| 5 | `05-components-data.prompt.md` | Build 7 data-display components for dashboards and model management |
| 6 | `06-components-nav.prompt.md` | Build 5 navigation components that form the app chrome |
| 7 | `07-motion-interaction.prompt.md` | Standardize all motion, hover states, focus rings, and loading patterns |
| 8 | `08-patterns-pages.prompt.md` | Build reference page implementations using the full component library |
| 9 | `09-documentation.prompt.md` | Generate living design system documentation |
