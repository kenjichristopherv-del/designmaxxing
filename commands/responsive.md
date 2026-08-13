---
description: Audit responsiveness — sweep 320→1920, catch overflow bugs and dead zones, grade the mobile nav, and verify the page survives touch, zoom, and real phone viewports.
argument-hint: "[route or scope, e.g. '/' or 'the app shell'] (optional)"
allowed-tools: Read, Grep, Glob, Bash, TodoWrite, mcp__playwright
---

# Responsiveness audit

Find where the layout breaks, snaps awkwardly, or quietly stops being designed as the viewport
changes. Follow the `design-review-method` skill for the severity rubric, the finding format,
and the anti-vibes rules. **This is read-only — do not edit any file.**

Scope: $ARGUMENTS

If no scope was given, audit the landing page and the primary working screen. This command is
mostly channel B — if no browser tool is connected, run the channel A checks only (viewport
units, `srcset`, media queries, hover-only patterns in source) and say plainly that no width
was actually rendered.

## Phase 1 — Sweep the widths

Screenshot each in-scope route at every ~100px from 320 to 1600, plus 1920. Save as
`route_WIDTHpx.png`, then review the strip:

- **Discontinuities.** Adjacent pairs that differ violently — a layout snapping rather than
  adapting — get flagged with both widths.
- **Dead zones.** Ranges where the layout is a stretched version of the previous breakpoint
  (the classic 768–1023 stretched-mobile band) — MED, with the range named. A dead zone means
  no one designed for those widths; tablets live there.

## Phase 2 — Hunt overflow

At 360, 390, 768, 1024, 1280, and 1440:

- `document.documentElement.scrollWidth > clientWidth` = page-level horizontal overflow —
  BLOCKER. Find the culprit by walking elements for rects extending past the viewport and name
  it with its selector and source.
- Tables, `<pre>`, code blocks, and long unbroken strings without an `overflow-x: auto`
  container — each is the next overflow bug waiting for wider content. MED at its `file:line`.

## Phase 3 — Grade the mobile experience

At 390×844:

- **The nav.** Is there a menu control? Open it: does it animate or teleport (feeds the Life
  Score), trap focus, close on Esc and backdrop tap, lock body scroll behind it? Are its
  targets ≥ 44px? Each miss is a separate finding — the nav is the most-used component on
  mobile.
- **Touch targets.** Interactive elements under 24×24 CSS px fail WCAG 2.2 (SC 2.5.8) —
  BLOCKER; under 44×44 on mobile is a HIGH best-practice miss. Adjacent targets closer than
  8px get flagged together.
- **Hover-only disclosure.** Tooltips, menus, or reveals that exist only on `:hover` with no
  tap path (channel A grep + attempt the tap): if content is unreachable on touch, HIGH.
  Check for `@media (hover: none)` handling.
- **Text at narrow widths.** At 320–360: word-broken headings, buttons wrapping to two lines,
  orphaned CTAs — MED each, with screenshots.

## Phase 4 — Fluid behavior and units

- **Fluid type.** Channel A: `clamp()` in font sizes. Channel B: computed h1 at 360 vs 1440 —
  identical-and-huge (overflowing phone) or identical-and-small (lost on desktop) both mean no
  fluid strategy; MED on marketing surfaces. Quote both computed sizes.
- **Viewport units.** `100vh` without a `dvh`/`svh` fallback is the mobile URL-bar bug (the
  bottom of the layout hides behind the browser chrome) — MED at each `file:line`. Fixed
  bottom bars without `env(safe-area-inset-*)` clip into the home indicator — LOW.
- **Container queries.** `@container` on reusable components is 2026-standard; absence is
  POLISH, but a component that breaks when rendered in a sidebar (resize test) is MED.
- **Zoom resilience.** Set zoom to 200% and re-run the overflow scan — breakage is a WCAG
  1.4.4 failure, BLOCKER.

## Phase 5 — Responsive images

- Channel A/D: `srcset`/`sizes`/`<picture>` presence; Lighthouse's responsive-images audit.
- Channel B: intrinsic vs rendered size per image. Ratio > 2× = shipping wasted bytes (MED);
  < 1× = upscaled and blurry (MED, confirm on the screenshot).

## Phase 6 — Report

Use the finding format from `design-review-method`, most severe first — every finding names
the width(s) where it holds, the selector, the source `file:line`, and the screenshot from the
sweep. Compute the Responsiveness category score (BLOCKER caps at 49).

Close with **Checked and clean** (widths that held) and **Not measured** (routes not swept,
orientations not tried, whether zoom and touch checks ran).

Then stop. Do not begin fixing. Offer `/designmaxxing:redesign`.

## Recommended next step

Close by printing one line — `→ Recommended next: …` — chosen by what you found:
- Overflow or zoom BLOCKERs → `/designmaxxing:redesign` scoped to them first — they're broken, not ugly.
- Dead zones and snap points → `/designmaxxing:redesign` scoped to the layout's breakpoint plan.
- Mobile nav teleports instead of moving → `/designmaxxing:motion`.
- Widths fine but the design is thin everywhere → `/designmaxxing:scan` for the full picture.
