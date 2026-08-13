---
description: Audit visual hierarchy and layout — spacing scale, alignment, density, above-the-fold clarity, all measured from computed styles, not vibes.
argument-hint: "[route or scope, e.g. '/dashboard' or 'the marketing pages'] (optional)"
allowed-tools: Read, Grep, Glob, Bash, TodoWrite, mcp__playwright
---

# Layout & hierarchy audit

Measure whether this UI has a spatial system or an accumulation of accidents. Follow the
`design-review-method` skill for the severity rubric, the finding format, and the anti-vibes
rules — every claim below carries a number and a screenshot. **This is read-only — do not edit
any file.**

Scope: $ARGUMENTS

If no scope was given, audit the app's key routes: the landing/home page, the primary working
screen, and one detail page. If no browser tool (Playwright MCP or Chrome DevTools MCP) is
connected or the app isn't running, run channel A only — grep the stylesheets and templates for
spacing values and container widths — and open the report by saying the render, alignment, and
density checks did not run.

## Phase 1 — Render and baseline

1. Start the app if needed; screenshot each in-scope route at 1440×900 and 390×844. Save to the
   scratch directory as `route_viewport.png`. These are the evidence every finding points at.
2. Detect the styling system: Tailwind config, CSS custom properties, CSS modules, styled
   components. Note where spacing is *supposed* to come from — that's the declared scale you'll
   diff against.

## Phase 2 — Measure the spatial system

Run these in the page via the browser tool's evaluate:

- **Spacing histogram.** Walk visible elements; collect every computed `margin-*`, `padding-*`,
  and `gap` value in px. Report: distinct value count (6–12 is a system; 30+ is no system), the
  % divisible by 4 (or by the detected base unit — infer it as the mode of pairwise GCDs of the
  common values), and the worst offenders with their selectors. Attribute off-scale values to
  source with a grep for the raw number.
- **Alignment clustering.** Collect `getBoundingClientRect().left` (and `right`) for top-level
  content blocks; cluster the x-coordinates. Edges within 1–8px of a cluster but not on it are
  near-misses — the misalignments an eye registers as "off" without knowing why. List each with
  both selectors and the pixel delta.
- **Container discipline.** Measure the max content width per section. Text containers wider
  than ~1200px, and sibling sections whose max-widths differ without reason (1140 vs 1200 vs
  1280), are findings — name both values.
- **Z-index histogram.** Collect declared `z-index` values (channel A grep plus computed spot
  checks). A ladder like 10/20/30 is a system; `9999` and `2147483647` are stacking-context
  hacks (an invisible layering trap that `z-index` can't escape) worth a LOW finding.

## Phase 3 — Judge the hierarchy

- **Above-the-fold clarity.** On the 1440×900 and 390×844 screenshots: is there one identifiable
  primary action? Count the elements competing at maximum visual weight (large AND bold AND
  saturated) — more than 2 means no hierarchy, and the count is the finding. Confirm the primary
  CTA (call to action — the one thing the page wants clicked) is the highest-contrast element
  above the fold.
- **Heading monotonicity.** Computed `font-size` must decrease h1 → h4, with exactly one h1 per
  page. Quote the sizes when they don't.
- **Ink ratio.** Per section, sum of element boxes vs section area. Above ~65% reads cramped;
  below ~15% reads barren (unless deliberately editorial — label that judgment Taste). Report
  the ratio per flagged section.
- **Vertical rhythm.** Section gaps should come from a small consistent set (e.g. 64/96/128),
  and intra-group spacing must be smaller than inter-group spacing — a caption belongs closer to
  its image than to the next block (Gestalt proximity: things near each other read as related).
  Detect violations by comparing sibling gaps and quote both numbers.

## Phase 4 — Component-level consistency

For each repeated component (same class/component name, ≥3 instances): diff computed padding,
`border-radius`, `box-shadow`, and `gap` across instances. Any divergence is drift — report the
census ("`.card`: 3 padding variants across 14 instances: 16px ×9, 20px ×4, 13px ×1") with the
source of each variant. Route a full system-wide census to `/designmaxxing:tokens`; here you
only flag what layout-level screenshots made visible.

## Phase 5 — Report

Use the finding format from `design-review-method`, most severe first, each with its measured
value, selector, source `file:line` where attributable, and screenshot path. Compute the Layout
category score (0–100, BLOCKER caps at 49) and state which surface profile you scored against
(marketing vs dashboard — density norms differ).

Close with:

- **Checked and clean** — the measurements that came back healthy.
- **Not measured** — routes not visited, viewports not rendered, channels that didn't run.

Then stop. Do not begin fixing. Offer `/designmaxxing:redesign`.

## Recommended next step

Close by printing one line — `→ Recommended next: …` — chosen by what you found:
- Spacing/alignment drift across components → `/designmaxxing:tokens` to census the whole system.
- Hierarchy fine but the page feels inert → `/designmaxxing:life`.
- Findings confirmed and a direction is needed → `/designmaxxing:brief`.
- Direction already clear → `/designmaxxing:redesign` scoped to the layout findings.
