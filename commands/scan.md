---
description: Full 4-channel design audit — scan the rendered UI, grade all nine categories, and hand back a findings ledger plus a prioritized redesign brief.
argument-hint: "[url or route to audit, e.g. 'http://localhost:3000' — add 'quick' or 'deep' to set depth]"
allowed-tools: Read, Grep, Glob, Bash, TodoWrite, mcp__playwright
---

# Full design scan

Grade this interface with evidence. Follow the `design-review-method` skill for the four
channels, the severity rubric, the scoring model, and the finding format. Every claim carries a
measurement; everything visual carries a screenshot.

**This is read-only — do not edit any file.** Screenshots and scratch notes go to the scratch
directory, named `route_viewport_state.png`.

Scope: $ARGUMENTS

If no target was given, find the dev server (check `package.json` scripts, running processes,
common ports) and audit the app's key routes. If the app can't run, or no browser tool is
connected (Playwright MCP or Chrome DevTools MCP both work), drop to the `quick` tier and open
the report with exactly what that means: no runtime pass, so hover coverage, computed censuses,
and every screenshot judgment are **Not measured** — findings come from source only.

## Phase 1 — Recon

Before measuring anything, learn what you're measuring.

1. **Detect the stack**: framework (Next/Vite/Rails/plain), styling system (Tailwind config,
   CSS modules, styled-components, vanilla CSS), token files (`tailwind.config.*`,
   `globals.css` custom properties, theme files), component library (shadcn, MUI, homegrown).
2. **Enumerate routes** from the router/pages directory and pick the audit set: the landing
   or home route, one dense screen (dashboard/table), one form, one detail page. List what
   you're skipping.
3. **Pick the surface profile** — marketing page or dashboard — and say so; it shifts the
   scoring weights (scroll-life and distinctiveness matter more on marketing; density norms
   differ on dashboards).
4. **Pick the tier.** `quick` = channel A only, ~2 minutes, no browser. `standard` (default)
   = adds runtime censuses and screenshots on the audit set at 360/768/1440. `deep` = the full
   grid: every route, dark mode, 200% zoom, throttled-network loading states.

## Phase 2 — Static pass (channel A)

Grep and read; no browser needed. This pass alone catches real defects:

- **Token drift**: raw hex colors and px values outside the token files; count them and name
  the worst files. `var(--*)` usage ratio vs hardcoded values.
- **Missing states in source**: interactive components with no `:hover`/`:focus-visible`
  styles anywhere, no loading/empty/error branches, forms with no designed validation.
- **Motion hygiene**: zero `@keyframes`, zero `transition` declarations, and no motion library
  = a statically dead UI; `transition: all` = lazy; no `prefers-reduced-motion` query when
  motion exists.
- **Slop tells in source**: stock Tailwind palette values, `from-blue-* to-purple-*` gradients,
  emoji in headings/buttons, `bg-clip-text` hero text (full list in `/designmaxxing:slop`).
- **Responsive smells**: `100vh` without `dvh` fallback, hover-only disclosure with no touch
  path, absent `srcset` on large images.

## Phase 3 — Runtime pass (channel B)

Drive the real app. Computed styles are the ground truth — judge them, not class names.

- **Censuses** per route: spacing histogram (all computed margin/padding/gap — % on the 4px
  grid, distinct-value count; healthy is ~6–12, 30+ is no system), font sizes fitted against
  modular ratios (1.125–1.618), color census converted to OKLCH (distinct values, hue
  families, near-duplicates under ΔE 2), radius census, shadow census, button style-signature
  census (more than 3–4 signatures for one semantic tier = system failure).
- **Hover coverage**: parse the CSSOM for `:hover`/`:focus-visible`/`:active` rules, match
  stripped selectors against interactive elements. Remember `getComputedStyle(el, ':hover')`
  does not work, and cross-origin stylesheets throw on `cssRules` — report those as unscanned.
- **Transition census**: `transitionDuration` on interactive elements; `"0s"` on hover-styled
  elements means instant state jumps — the most reliable "feels dead" metric.
- **Overflow sweep** at 360/768/1024/1440: `scrollWidth > clientWidth` at page level is a
  BLOCKER; per-element rects past the viewport get listed with selectors.
- **Theme and zoom** (deep tier): emulate `prefers-color-scheme: dark` and re-census; set 200%
  zoom and re-run the overflow sweep.

## Phase 4 — Tooling pass (channel D)

Run what's installed rather than reimplementing it: Lighthouse (CLS, LCP, tap targets, font
display) and axe-core (contrast, labels, heading order) if available — `npx lighthouse`,
`npx @axe-core/cli`, or the app's own tooling. WCAG 2.2 AA is the compliance bar; APCA, if
run, is advisory "perceptual contrast" only. If neither tool is available, say so under **Not
measured** instead of guessing at contrast ratios.

## Phase 5 — Vision pass (channel C)

Screenshot the grid — audit routes × 360/768/1440 × light/dark (tier permitting) — then judge
each screenshot against the rubric for what metrics can't see: above-the-fold clarity (one
primary action, or several shouting?), hierarchy (does size/weight/color order match importance
order?), whitespace quality (margins vs gutters), alignment (edges that almost line up),
genericness (does this look like every AI-built page?). Every judgment here is either backed by
a measurement from Phase 3 or labeled **Taste**.

## Phase 6 — Score and report

1. Compute the nine category scores, the Life Score and Distinctiveness composites (formulas
   in `design-review-method`; the dedicated commands go deeper), apply the caps — any BLOCKER
   caps its category at 49, an accessibility BLOCKER caps Overall at 59 — and assign the
   letter grade.
2. Findings ledger, most severe first, in the skill's finding format — plain-English line,
   Where, Measured, Evidence, Fix, Verify.
3. Close with **Scores**, **Checked and clean**, and **Not measured** — never round the last
   one to zero.
4. **The redesign brief**, one page: the direction (what this product should feel like, argued
   from its own domain and brand, not from trends) and the five highest-leverage moves in
   order, each tied to the findings and the category it moves.

Then stop. Do not begin fixing. Offer `/designmaxxing:redesign`.

## Recommended next step

Close by printing one line — `→ Recommended next: …` — chosen by what you found:

- Direction is obvious and findings are mechanical → `/designmaxxing:redesign` with the brief.
- Findings are real but the direction needs taste decisions → `/designmaxxing:brief`.
- Life Score dragged the grade down → `/designmaxxing:life` for the per-element evidence.
- Distinctiveness under 60 → `/designmaxxing:slop` for the full tell list before redesigning.
- Token drift dominated the ledger → `/designmaxxing:retheme`.
- One category is the clear outlier → its dedicated command (`/designmaxxing:type`,
  `/designmaxxing:color`, `/designmaxxing:layout`, `/designmaxxing:responsive`,
  `/designmaxxing:states`, `/designmaxxing:motion`).
- Grade A and nothing actionable → say so, and name the one POLISH item worth doing anyway.
