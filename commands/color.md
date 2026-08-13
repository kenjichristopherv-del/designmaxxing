---
description: Audit color — census the palette that actually renders, hold it to WCAG 2.2 AA, judge the neutrals and the dark mode, and catch the drift a designer would.
argument-hint: "[route or scope, e.g. '/app' or 'dark mode only'] (optional)"
allowed-tools: Read, Grep, Glob, Bash, TodoWrite, mcp__playwright
---

# Color audit

Census every color the UI actually paints, then judge coherence, compliance, and craft — in
that order. Follow the `design-review-method` skill for the severity rubric, the finding
format, and the anti-vibes rules. **This is read-only — do not edit any file.**

Scope: $ARGUMENTS

If no scope was given, audit the key routes in both light and dark themes. If no browser tool
is connected, run channel A only — declared colors in stylesheets, tokens, and config — and say
plainly that the rendered census, contrast measurements, and dark-mode emulation did not run.

## Phase 1 — Census and cluster

In the running page, collect every computed `color`, `background-color`, and `border-color`;
convert to OKLCH (a color space where distance matches what eyes perceive) and cluster:

- Report: distinct values, hue families, clusters. A healthy product UI is 1 brand hue, 1–2
  accents, one neutral ramp, and a semantic set (success/warn/error/info). 6+ unrelated hue
  families is incoherence — name them.
- **Near-duplicates.** Pairs within ΔE < 2 (visually identical) both in use — `#f4f4f5` and
  `#f5f5f5` — are token drift. List each pair with the selectors using them; this feeds
  `/designmaxxing:tokens`.
- **Framework-default detection.** Match computed hexes (not class names — people copy the
  palette into custom CSS) against the stock Tailwind sets: slate `#f8fafc #f1f5f9 #e2e8f0
  #64748b #1e293b #0f172a`, blue-500 `#3b82f6`, indigo/violet/purple `#6366f1 #8b5cf6
  #a855f7`, pink-500 `#ec4899`. Exact hits mean shipped defaults — a MED finding here and
  evidence for `/designmaxxing:slop`.

## Phase 2 — Contrast: compliance, then perception

- **WCAG 2.2 AA is the bar.** Run axe-core (channel D): 4.5:1 body text, 3:1 large text and
  non-text UI components. Every failure is a BLOCKER with the measured ratio, both hexes, and
  the selector. An accessibility BLOCKER caps the overall score at 59 — say so in the report.
- **APCA advisory pass.** Run `apca-w3` as a second opinion, labeled "perceptual contrast —
  advisory, not compliance": Lc ≥ 75 body, ≥ 60 large, ≥ 45 large bold. It catches thin light
  text that ratio math wrongly passes and dark-mode pairs it wrongly fails. Never report APCA
  as a compliance result.

## Phase 3 — Semantics and neutrals

- **Semantic discipline.** Red/green/amber used as decoration, or errors rendered in the brand
  color, breaks the meaning users assign to those hues — MED, with each misuse quoted. Check
  that a semantic token set exists (`--color-danger` or equivalent) rather than raw reds
  sprinkled through source.
- **Pure #000 / #fff.** `#000` body text on `#fff` is harsh (near-blacks `#111–#1a1a1a` or a
  hue-tinted dark read better) — LOW. A "dark mode" that is pure `#000` behind pure `#fff` text
  is the no-design-pass giveaway — MED.
- **Neutral quality.** Are the grays hue-tinted toward the brand (chosen) or stock framework
  values (inherited)? A Taste-labeled note either way.

## Phase 4 — Dark mode

Emulate `prefers-color-scheme: dark` via the browser tool and screenshot the grid:

- Does dark mode exist at all? If the app has themes, is `color-scheme` set so form controls
  and scrollbars follow?
- **Naive inversion check.** Shadows that vanish (shadows don't read on dark — elevation must
  come from lighter surfaces), borders that disappear, images left glaring, elevated surfaces
  *darker* than the base (backwards: elevation goes lighter in dark mode). Each is MED with a
  screenshot.
- Re-run the contrast pass in dark — a pair passing in light and failing in dark is a
  dark-mode BLOCKER, filed as such per the anti-vibes rules.

## Phase 5 — Gradients and accent discipline

- Extract gradient stops (channel A + computed). Saturated hue pairs that cross the gray
  dead-zone without a midpoint (blue→orange) go muddy in the middle; long two-stop gradients
  band. LOW each, with the stops quoted.
- **Accent coverage.** Estimate the % of viewport pixels carrying the accent color; past ~10%
  it stops reading as an accent — quote the estimate.

## Phase 6 — Report

Use the finding format from `design-review-method`, most severe first — every finding carries
both hexes, the measured ratio or ΔE, the selector, source `file:line`, and screenshot.
Compute the Color category score (BLOCKER caps at 49; a11y BLOCKER caps Overall at 59).

Close with **Checked and clean** and **Not measured** (themes not emulated, routes skipped,
whether the APCA pass ran).

Then stop. Do not begin fixing. Offer `/designmaxxing:redesign`.

## Recommended next step

Close by printing one line — `→ Recommended next: …` — chosen by what you found:
- Near-duplicate ramp or framework defaults confirmed → `/designmaxxing:tokens`, then `/designmaxxing:retheme`.
- Contrast BLOCKERs → `/designmaxxing:redesign` scoped to the failing pairs, before anything aesthetic.
- Palette coherent but characterless → `/designmaxxing:brief` for a palette direction.
- Dark mode absent or naive → `/designmaxxing:redesign` scoped to a tokenized dark theme.
