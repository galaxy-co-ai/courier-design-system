# Courier Design System — Bootstrap

> **Give this entire prompt to Claude Code.** It reads the repo and wires everything up.

---

```
I just cloned the courier-design-system repo. I need you to set it up in my Courier project.

Here's what to do:

1. First, find my Courier project root. Look for:
   - A Next.js project with next.config.ts/js
   - package.json with "next" as a dependency
   - An existing src/ directory
   If you can't find it, ask me for the path.

2. Read everything in the courier-design-system repo:
   - README.md (overview)
   - INDEX.md (file map)
   - CLAUDE.md (project instructions)
   - constitution/ENGINEERING-CONSTITUTION.md
   - constitution/QUICK-REFERENCE.md
   - skills/ui-engineering/SKILL.md
   - skills/ui-engineering/references/design-tokens.md
   - skills/ui-engineering/references/component-patterns.md
   - skills/ui-engineering/references/quality-checklist.md
   - product-brief/COURIER-PRODUCT-BRIEF.md

3. Copy these into my Courier project:

   a. CLAUDE.md → project root (merge with existing if one exists — don't overwrite)

   b. skills/ → project root
      courier-project/
      └── skills/
          └── ui-engineering/
              ├── SKILL.md
              └── references/
                  ├── design-tokens.md
                  ├── component-patterns.md
                  └── quality-checklist.md

   c. constitution/ → project root
      courier-project/
      └── constitution/
          ├── ENGINEERING-CONSTITUTION.md
          └── QUICK-REFERENCE.md

   d. product-brief/ → project root
      courier-project/
      └── product-brief/
          └── COURIER-PRODUCT-BRIEF.md

4. Update the CLAUDE.md paths to be relative to the project root:
   - constitution/ENGINEERING-CONSTITUTION.md
   - skills/ui-engineering/SKILL.md
   - skills/ui-engineering/references/*
   - product-brief/COURIER-PRODUCT-BRIEF.md
   - design-system/prompts/* (keep these in the cloned repo — reference by path)

5. Verify the setup:
   - Read the copied CLAUDE.md and confirm all paths resolve
   - Read the SKILL.md and confirm it will trigger on UI work
   - List all copied files to confirm nothing was missed

6. Tell me:
   - What was copied and where
   - Where the design system prompts live (they stay in the cloned repo)
   - How to run the first prompt (00-brand-audit)
   - Remind me to read the product brief first

Done. After this, I can feed you the design system prompts one at a time
from design-system/prompts/ (00 through 09, in order).

START WITH: "Read product-brief/COURIER-PRODUCT-BRIEF.md before anything
else. Then start prompt 00."
```
