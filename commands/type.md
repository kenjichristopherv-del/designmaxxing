---
description: Audit typography — what actually renders, whether the sizes form a scale, whether the text is comfortable to read, and whether the fonts load like professionals shipped them.
argument-hint: "[route or scope, e.g. '/blog' or 'the whole app'] (optional)"
allowed-tools: Read, Grep, Glob, Bash, TodoWrite, mcp__playwright
---

# Typography audit

Judge the type that renders, not the type that was declared. Follow the `design-review-method`
skill for the severity rubric, the finding format, and the anti-vibes rules. **This is
read-only — do not edit any file.**

Scope: $ARGUMENTS

If no scope was given, audit the text-heaviest route plus the primary working screen. If no
browser tool is connected, run channel A only — `@font-face`/`next/font` declarations, declared
sizes and stacks — and say plainly that the census, fallback detection, and FOUT checks did not
run.

## Phase 1 — Census what renders

In the running page, walk visible text nodes and collect computed `font-family`, `font-size`,
`font-weight`, `line-height`, and `letter-spacing`:

- **Fallback detection.** Text rendering in Times, Arial, or the bare system stack when a
  webfont was declared means the font never loaded — that's a BLOCKER, not a style note. Confirm
  via `document.fonts` (the browser's registry of actually-loaded faces).
- **Generic stack detection.** Inter, Poppins, Roboto, or Geist as the *only* face, default
  weights, no display font — a MED genericness finding that also feeds `/designmaxxing:slop`.
- **Weight census.** Only 400 everywhere = flat hierarchy. Four weights inside one paragraph =
  noise. A weight in use that `document.fonts` never loaded = faux bold (the browser smearing a
  fake bold onto a face that doesn't have one) — quote the weight and the loaded set.

## Phase 2 — Test the scale

- Histogram the computed sizes. Fit them least-squares against the modular ratios 1.125, 1.2,
  1.25, 1.333, 1.414, 1.5, 1.618. A healthy system is 5–8 sizes on one ratio; 12+ arbitrary
  sizes is no scale — report the full list and the best-fit ratio with its error.
- **h1:body ratio.** Under ~1.5× on a marketing page is timid; over ~4× on a dense app is
  shouting. State the surface profile you judged against.
- Attribute off-scale sizes to source (`grep` the raw px/rem values) so the fix list has
  `file:line` targets.

## Phase 3 — Measure reading comfort

- **Line-height.** Body: flag outside 1.4–1.7 (computed `lineHeight / fontSize`). Multi-line
  headings above 32px: flag above 1.3, and flag clipped descenders from over-tight values.
- **Line length.** For prose blocks, estimate characters per line (`rect.width / (fontSize ×
  0.5)` is close enough; measure the actual font via canvas when it matters). 45–90ch is the
  comfortable band; over 100ch on prose is HIGH — quote the measured ch and the container.
- **The light-gray fashion failure.** Primary body copy at `opacity < 0.6` or a light gray on
  white is a legibility finding even when it technically passes contrast — measure the ratio
  and say which it is: a compliance failure (BLOCKER, route to `/designmaxxing:color`) or a
  comfort failure (MED).
- `text-align: justify` without `hyphens: auto` produces rivers of whitespace — LOW.

## Phase 4 — Refinement and loading

- **Letter-spacing at the extremes.** Display text over 40px with default tracking wants
  −0.01 to −0.03em; ALL-CAPS labels want positive tracking (~+0.05em). Both are LOW findings
  with the computed value quoted.
- **`text-wrap`.** Absence of `balance` on headings and `pretty` on prose is a LOW finding —
  they're one-line fixes and their absence signals no typographic pass happened.
- **Loading behavior.** Channel A: `font-display` strategy, `<link rel="preload">` for font
  files, `size-adjust` fallback metrics. Channel B: screenshot at first paint and after
  `document.fonts.ready`; a visible diff is FOUT (a flash of fallback text before the real font
  arrives) — MED, and it also feeds layout shift in `/designmaxxing:scan`'s perf category.

## Phase 5 — Report

Use the finding format from `design-review-method`, most severe first — every finding names the
selector, the measured value vs the expected band, the source `file:line`, and the screenshot.
Compute the Typography category score (BLOCKER caps at 49).

Close with:

- **Checked and clean** — e.g. "scale fits 1.25 within 2%, line lengths 62–78ch, all weights
  loaded."
- **Not measured** — routes, themes, or channels skipped.

Then stop. Do not begin fixing. Offer `/designmaxxing:redesign`.

## Recommended next step

Close by printing one line — `→ Recommended next: …` — chosen by what you found:
- Generic single-face stack confirmed → `/designmaxxing:brief` for a type pairing direction.
- Off-scale sizes scattered through source → `/designmaxxing:tokens`, then `/designmaxxing:retheme`.
- Contrast questions raised → `/designmaxxing:color`.
- Findings ready to act on → `/designmaxxing:redesign` scoped to type.
