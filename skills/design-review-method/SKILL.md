---
name: design-review-method
description: >
  The evidence discipline for any frontend design review, UI audit, redesign, or visual
  critique. Use this skill whenever judging or improving how an interface looks, feels, or
  behaves: a design review, a UI audit, "make this look better", "make it more alive",
  "this looks generic", a color/typography/layout/spacing critique, a dark-mode or
  responsive check, or a redesign of an existing screen. Trigger on 'design review',
  'redesign', 'ugly', 'looks bad', 'looks generic', 'AI slop', 'feels dead', 'polish the
  UI', 'improve the design', 'hover states', 'spacing', 'typography', 'color palette',
  'design system', 'design tokens'. Defines the measurement-before-critique rule, the four
  detection channels, the severity rubric, the scoring model, and the finding format that
  every designmaxxing command depends on.
---

# Design Review Method

A design critique you cannot measure is a mood. It reads as insight, changes with the
reviewer, and gives the person holding the code nothing to act on. This skill defines how to
review design so that every claim survives being checked — and how to redesign so the
improvement is provable instead of asserted.

## The one rule

**No critique without a measurement. No redesign without a before/after.**

Both halves are load-bearing:

- **A measurement** — every finding names the thing (a selector, a component, a `file:line`),
  the number you measured (the computed value, the count, the coverage percentage, the
  contrast ratio), and what the number should be. For anything visual, it carries a
  screenshot. "The spacing feels inconsistent" is a mood; "27 distinct padding values, 41%
  off the 4px grid, worst offenders `.card` (13px) and `.nav` (17px)" is a finding.
- **A before/after** — every applied change re-renders the page and shows the same
  screenshot grid before and after, with the score delta. A redesign that ends with "done!"
  and no evidence is a claim, not a result.

Judgment still has a place — hierarchy, balance, and personality are real and partly
unquantifiable — but judgment gets labeled **Taste** and stays separate from measured
findings, so the reader always knows which claims are checkable and which are a
point of view.

## The four channels

Every check runs on one or more of these. Say which channels ran, because each catches what
the others cannot:

| Channel | What it is | Uniquely catches |
|---|---|---|
| **A — Static** | Grep/AST over CSS, Tailwind classes, JSX/TSX, token files | Hardcoded values, token drift, missing `prefers-reduced-motion`, slop tells, states that don't exist in source |
| **B — Runtime** | Playwright + CDP against the running app: computed styles, CSSOM, real input events | Ground truth: spacing/type/color censuses, hover-state coverage, transition durations, overflow, `document.getAnimations()` |
| **C — Vision** | Screenshots (route × breakpoint × state) judged against the rubric | Hierarchy, balance, whitespace quality, genericness — what no metric captures |
| **D — Tooling** | Lighthouse, axe-core, APCA | Contrast, CLS, tap targets, font flash. Never reimplement these; orchestrate them |

**Degrade honestly.** If the app isn't running or no browser tool is connected, channel A
still works — run it, and open the report by saying exactly which channels did not run and
what that means ("no runtime pass: hover coverage and computed censuses are unmeasured, so
findings below are from source only"). Never present a static-only scan as a full one.

Channel B mechanics that save an hour each (verified, current as of 2026):

- `getComputedStyle(el, ':hover')` **does not work** — pseudo-class introspection is an
  unimplemented proposal. Get hover truth two ways: parse `document.styleSheets` CSSOM for
  `:hover`/`:focus-visible`/`:active` selectors and match them against interactive elements
  (`el.matches(selectorWithPseudoStripped)`), and trigger real hovers — Playwright input is
  trusted, so `page.hover()` genuinely applies `:hover`; diff computed styles and a
  screenshot crop ~350ms later (and once at ~16ms, to tell a transition from a jump).
- Cross-origin stylesheets throw on `.cssRules` — catch it and report those sheets as
  unscanned rather than silently skipping them.
- Tailwind-default detection must match **computed hex values**, not class names — people
  copy the palette into custom CSS.
- Contrast compliance is **WCAG 2.2 AA** (4.5:1 text, 3:1 large text and UI components).
  APCA runs as a second, advisory pass, labeled "perceptual contrast" — it is not in any
  normative standard yet and must never be reported as compliance.

## Anti-vibes rules

These exist because the default failure mode of AI design review is confident, plausible,
unfalsifiable taste — and the default failure mode of AI redesign is replacing one generic
look with another.

1. **Render before you judge.** Screenshot the running app before reading a line of source.
   Source shows intent; the render shows what users get, and the two diverge constantly —
   a font that never loaded, a media query that never matches, a hover rule shadowed by
   specificity.
2. **Never report a feeling without a number.** If you catch yourself writing "cluttered",
   "inconsistent", "dated", or "unpolished", stop and measure the thing that produced the
   feeling. If it genuinely can't be measured, label it **Taste**.
3. **Judge computed styles, not class names.** `text-gray-500` tells you what the author
   asked for; `getComputedStyle` tells you what happened after the cascade, the override,
   and the dark-mode variant had their say.
4. **Measure across states, themes, and widths before filing.** A finding that only exists
   in dark mode is a dark-mode finding. A layout that only breaks at 768px is a breakpoint
   finding. Say where it holds and where it doesn't.
5. **Extract the project's intent before redesigning toward your own.** Read the existing
   tokens, the brand assets, the CLAUDE.md, the best screen in the app. A redesign should
   make the product more itself, not more like your defaults — swapping their look for the
   AI-default look is the failure this plugin exists to prevent.
6. **The user's stated direction always wins.** If they asked for the purple gradient, the
   purple gradient is correct. Note the trade-off once, then execute it well.
7. **Trend is not quality.** "Bento grids are popular" is not a finding. Tie every
   recommendation to an effect on the reader: scannability, feedback, credibility,
   comprehension.
8. **Don't average away a blocker.** A beautiful page with invisible focus states is not
   "8/10 overall" — severity caps exist because one blocker outweighs any amount of polish.
9. **Count the whole population, not the first example.** "Buttons are inconsistent" needs
   the census: how many button style-signatures exist, out of how many buttons. One odd
   button in eighty is a nitpick; five signatures across eighty is a system failure.
10. **Zero findings is a valid result.** A well-designed screen gets told so. Do not
    manufacture nitpicks to justify the scan — and do not inflate POLISH items to MED to
    make the report look substantial.
11. **Preserve the evidence.** Save every screenshot you judged to the scratch directory
    with a predictable name (`route_viewport_state.png`) and reference them by path in the
    report. A finding whose screenshot is gone is unverifiable.

## Severity rubric

| Severity | Bar |
|---|---|
| **BLOCKER** | Broken or exclusionary: page-level overflow, failed WCAG 2.2 AA contrast, invisible focus, keyboard-unreachable controls, unreadable text. Caps its category at 49; an accessibility BLOCKER caps the overall score at 59. |
| **HIGH** | Clearly hurts usability or credibility: no hover feedback anywhere, blank screen while loading, primary CTA indistinguishable, mobile nav unusable, body text 120ch wide. |
| **MED** | Noticeably unpolished to a non-designer: off-scale spacing, 12 arbitrary font sizes, drifting radii, spinner where a skeleton belongs. |
| **LOW** | Refinement a designer would catch: default letter-spacing on display text, missing `text-wrap: balance`, harsh pure-black on white. |
| **POLISH** | Taste-level: easing curves, entrance choreography, texture. Report these, but never inflate them. |

## Scoring model

Nine categories, each 0–100, reported individually and blended into an overall grade:

```
Life 18 · Layout 15 · Typography 15 · Color 12 · Accessibility 12 ·
Responsiveness 10 · Consistency 8 · Perceived performance 5 · Distinctiveness 5

Overall = weighted mean, then apply caps:
  any BLOCKER caps its own category at 49
  any accessibility BLOCKER caps Overall at 59
Grades: A ≥ 90 · B ≥ 80 · C ≥ 70 · D ≥ 55 · F below
```

Weights flex by surface: on a marketing page, scroll-life and distinctiveness matter more;
on a dashboard, density norms differ and decoration matters less. Say which profile you
scored against.

Two composite scores get computed from fixed formulas so they're comparable across runs:

- **Life Score (0–100)** — hover coverage 25 · transition coverage 25 · loading/empty/error
  design 20 · focus states 10 · animation census 10 · reduced-motion respect 5 ·
  view-transition/scroll extras 5. Owned by `/designmaxxing:life`.
- **Distinctiveness (0–100)** — start at 100, subtract weighted slop tells (stock framework
  palette matched on computed hexes, template gradients, emoji-as-icons, the default
  typeface alone, the canned hero-plus-three-cards skeleton), add back for positive identity
  signals (a real type pairing, hue-tinted neutrals, bespoke illustration, a motion
  identity). Bands: 85+ distinctive · 60–84 competent but derivative · 35–59
  template-adjacent · below 35, slop. Owned by `/designmaxxing:slop`.

## Finding format

Report every finding in this shape, most severe first:

```
[BLOCKER | HIGH | MED | LOW | POLISH | Taste] Short, specific title
In plain English: one sentence a non-designer could follow, before any detail —
              "Nothing on this page reacts when you point at it, so it feels frozen."
Where:        selector or component, and the source that produces it — path/to/file.ext:120
Surface:      route + viewport + theme + state where this holds (and where it doesn't)
Measured:     the number — computed value, count, coverage %, ratio — vs what it should be
Evidence:     screenshot path(s), quoted computed style, census excerpt
Why it matters: the effect on a real user, in one sentence — feedback, legibility, trust
Fix:          the specific change, as code or as an exact token/value move
Verify:       what to re-measure after the fix, and the number that should come back
```

**Lead with the plain-English line, always.** The report goes to whoever asked, not to a
designer. Translate jargon in the same breath the first time it appears: "hover state (what
the element does when you point at it)", "tokens (the named values a design system reuses
instead of raw numbers)", "CLS (the score for how much the page jumps around while
loading)". One clause each, then use the term freely.

Close every report with:

- **Scores** — the category scores and composites that changed or were computed.
- **Checked and clean** — what was measured and found healthy. Coverage evidence, and the
  reader's reason to trust the findings list is complete.
- **Not measured** — channels that didn't run, routes not visited, states not reached.
  Never round this to zero.

## Redesign discipline

The loop is **see → judge → change → prove**, and the writing commands
(`/designmaxxing:redesign`, `/designmaxxing:retheme`) follow it strictly:

1. **Baseline first.** Screenshot the grid (routes × breakpoints × key states) before
   touching anything. No baseline, no redesign.
2. **Tokens before components, components before motion.** Changing the system first means
   every component change lands on stable ground; motion goes last because it depends on
   both.
3. **One coherent direction, not a pile of fixes.** Fixes cluster under a stated direction
   (from `/designmaxxing:brief` or the scan's redesign brief) so the result is a design,
   not a patchwork.
4. **Re-render and re-score after each cluster.** Any category that regresses gets that
   cluster revised or reverted before moving on — a redesign that trades a layout win for a
   contrast loss is not converging.
5. **Prove it.** End with the before/after contact sheet, per breakpoint, plus the score
   deltas. That artifact *is* the deliverable; the prose summary just narrates it.
6. **Match the codebase.** Existing framework, component patterns, naming, token
   conventions. A redesign written in a foreign idiom gets reverted no matter how it looks.

## Scope discipline

Review commands are read-only: screenshots, computed-style probes, and scratch files are
fine; application code is not touched. Only `redesign` and `retheme` write, and only after
a baseline exists. Never run a review against a production system in a way that mutates
data — audit flows on dev/staging seeds, not by submitting real forms. Screenshots may
contain real user data when the app is seeded from production — treat them as sensitive,
keep them in the scratch directory, and never publish them anywhere.

## Always end with the next step

Every command closes with one line — `→ Recommended next: …` — chosen by what was found.
The through-line is **see → judge → change → prove**:

- Scan done, findings confirmed → `/designmaxxing:brief` for a direction, or
  `/designmaxxing:redesign` when the direction is already clear.
- Life Score low → `/designmaxxing:redesign` scoped to states and motion.
- Token drift found → `/designmaxxing:retheme`.
- Redesign applied → re-run the command that found the problems, and confirm the scores
  moved.
- Everything healthy → say so, and point at the one POLISH item worth doing anyway.

Tailor it to what you actually found. Never end a report without it.
