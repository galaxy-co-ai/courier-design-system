# Courier Quick Reference

> One-screen cheat sheet. Full spec: `ENGINEERING-CONSTITUTION.md`

---

## Token Naming

| Category | Token | Value / Usage |
|---|---|---|
| Background | `--background` | `slate-950` — page base |
| Surface | `--surface` | `slate-900` — cards, panels |
| Surface raised | `--surface-raised` | `slate-800` — elevated elements |
| Border | `--border` | `slate-700/50` — subtle separators |
| Primary | `--primary` | Cyan — actions, links, positive status |
| Secondary | `--secondary` | Amber — warnings, attention states |
| Foreground | `--foreground` | `slate-50` — primary text |
| Muted | `--muted-foreground` | `slate-400` — secondary text, metadata |
| Destructive | `--destructive` | Red — errors, delete actions |

## Component Rules

1. **Server Components by default.** Client Components only when you need `useState`, `useEffect`, or event handlers.
2. **shadcn/ui as base.** Extend via CSS variables. Don't wrap in abstraction layers.
3. **Monospace for data.** Model names, stats, IDs, code — always `font-mono`.
4. **No barrel exports.** Import directly: `@/components/model-card` not `@/components`.
5. **Colocate everything.** Component + types + tests in the same directory.

## Quality Quick-Checks

Before every PR:

- [ ] Dark theme tested first (not light mode afterthought)
- [ ] Zero TypeScript errors, zero `any`
- [ ] Loading + error states exist for every async op
- [ ] All interactive elements keyboard-reachable with visible focus
- [ ] No `console.log` left in committed code

## Decision Shortcuts

| Situation | Action |
|---|---|
| Low-cost change (CSS, copy) | Ship it, learn from usage |
| Medium-cost (new pattern, API shape) | Prototype first |
| High-cost (architecture, schema) | Write it down, sleep on it |
| "Should I add this feature?" | Does it serve model loading, API serving, or monitoring? No → skip it |
| Stuck for 30+ min | Zoom out → break down → timebox → check upstream → walk away |

## Priority Stack

1. Stability (no crashes, no data loss)
2. Core UX (model loading, API, monitoring)
3. Polish (transitions, error messages)
4. New features (only after 1-3 are solid)

## Anti-Patterns (Never)

| Don't | Do Instead |
|---|---|
| Shadows on dark bg | Surface luminance + `1px` borders |
| Animate tab switches / list updates | Reserve motion for user-initiated transitions |
| Soften technical language | Say "7B Q4_K_M", not "your smart model" |
| `rounded-full` everywhere | `rounded-md` cards, `rounded-sm` inputs, sharp tables |
| Marketing copy in product UI | Functional labels only |
| Hide config behind "simple mode" | Surface knobs with smart defaults |
| Rainbow chart colors | Perceptually uniform palette, max 6 hues |

---

*Full constitution: `ENGINEERING-CONSTITUTION.md` | Fonts: HubotSans Black (headings) + GeneralSans (body) | Dark-first, always.*
