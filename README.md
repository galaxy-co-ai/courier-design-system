# Courier Design System

> From one bootstrapped founder to another.

## What This Is

A complete design system toolkit custom-built for Courier. It gives your Claude Code the design instincts, quality standards, and build sequence to produce studio-quality UI that matches your product's identity — dark, data-dense, technically honest. No generic templates. Every token, pattern, and prompt was written specifically for Courier.

## What's Inside

### 1. Engineering Constitution

Your quality standards, decision framework, and anti-patterns list — adapted from a system running across 10+ production projects. It defines what Courier UI should feel like (Linear + Kalshi), what it should never do (marketing copy in product chrome, shadows on dark backgrounds), and how to make consistent decisions under pressure.

### 2. Claude Code Skills Package

A skill that triggers automatically on any UI work and teaches Claude Code Courier's design language — the cyan/amber two-accent system, border-based depth model, GeneralSans + HubotSans typography, component patterns, and interaction philosophy. Once installed, Claude Code stops guessing and starts building like it knows your product.

### 3. Design System Build Sequence

10 executable prompts you run in order. Start with a brand audit of your existing codebase, then build up through colors, typography, spacing, components (core, data, navigation), motion, page patterns, and documentation. By the time you finish, you have a complete, rationalized design system — not a mood board.

### 4. Product Design Brief

This is the most important file in the repo. It's based on a real interview with a power user who's been running Courier in production for over a month across multiple real projects. The brief captures what Courier does exceptionally well, where the gaps are, and where the product should go next. Read it before building new features. It might change what you build next. That's the intent.

## How to Use It

### Quick Start

1. Clone this repo
2. Give `BOOTSTRAP.md` to your Claude Code — it reads the repo and wires everything into your Courier project automatically
3. Read the product brief (`product-brief/COURIER-PRODUCT-BRIEF.md`)
4. Run the design system prompts in order (`design-system/prompts/00-09`)

### Manual Setup (if you prefer)

1. Read the product brief first (`product-brief/COURIER-PRODUCT-BRIEF.md`)
2. Copy `CLAUDE.md` into your Courier project root
3. Copy `skills/`, `constitution/`, and `product-brief/` into your project
4. Run the design system prompts in order (`design-system/prompts/00-09`)

### The Promise

By the time you run all 10 prompts, you'll have:

- A rationalized color system (OKLCH, 12-step gray scale, your cyan + amber identity preserved)
- A complete type scale with your GeneralSans + HubotSans fonts
- A spacing and layout system with app shell
- 25+ components (core + data + navigation)
- A motion and interaction system
- 4 reference page implementations
- Full documentation

### A Note About the Product Brief

Read `product-brief/COURIER-PRODUCT-BRIEF.md` before anything else. It's based on a real interview with someone who uses Courier every day to build real products. The insights in there — especially around the gap between model-centric and project-centric thinking — might reshape your roadmap. That's the intent.

## Credits

Built by Dalton Cox ([@GalaxyCo.ai](https://galaxyco.ai)) using Claude Code.

Courier is Jackson's product. These are tools to help him build it faster and sharper.

---

*Ship fast. Ship sharp. Ship local.*
