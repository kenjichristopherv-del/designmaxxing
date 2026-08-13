---
description: Apply the design direction in-repo — re-render, screenshot-diff every breakpoint, and iterate until the scores converge. The only command that redesigns code.
argument-hint: "[the direction or scope, e.g. 'the brief above' or 'just the dashboard cards' — defaults to the most recent brief or scan findings]"
allowed-tools: Read, Grep, Glob, Bash, Edit, Write, TodoWrite, mcp__playwright
---

# Redesign

Execute the loop: **see → judge → change → prove.** Follow the `design-review-method` skill for
the evidence bar, the redesign discipline, and the finding format — its rule binds every phase
here: **no critique without a measurement, no redesign without a before/after.**

**This command writes code — presentation code only.** It edits styles, tokens, markup
structure, class lists, and motion. It does not touch application logic, data fetching, routing
behavior, or business rules, and it never introduces a new styling system or dependency without
asking first.

Scope: $ARGUMENTS

If no scope was given, execute the most recent direction in the conversation — the brief from
`/designmaxxing:brief` or the redesign brief a scan produced. If neither exists, say so and run
Phase 2's gate before touching anything.

## Phase 1 — Baseline

**No baseline, no redesign.** The before/after is the deliverable; without a "before" there is
nothing to prove against.

1. Identify the affected routes from the scope. Screenshot each at 360, 768, and 1440 minimum —
   plus any breakpoint the findings named — in both themes if the app has them. Save to the
   scratch directory with predictable names (`route_viewport_theme.png`). This is the "before"
   set; do not overwrite it later.
2. Record the current numbers you intend to move: category scores from the most recent scan, or
   a quick re-measure of the specific figures the findings cite (hover coverage %, contrast
   pairs, spacing histogram, distinct font sizes). Converging is only claimable against these.
3. If no browser tool is connected, **stop and say so.** Offer to proceed static-only, and name
   the cost plainly: the proof becomes code-level diffs instead of rendered before/afters — a
   materially weaker guarantee. Proceed only on explicit go-ahead.

## Phase 2 — Direction

Resolve the one direction this redesign executes. In order of authority: the user's words in
the scope, then the `/designmaxxing:brief` output, then the redesign brief from the last scan.

- **One coherent direction, not a pile of fixes.** Every change in Phase 3 must serve it. If
  the findings are aesthetic (generic look, weak hierarchy, no personality) and no direction
  exists, stop and offer `/designmaxxing:brief` first — redesigning toward your own defaults is
  the failure this plugin exists to prevent.
- The exception: **BLOCKER and HIGH mechanical findings proceed without a brief.** Failed
  contrast, page overflow, invisible focus, dead hover states — these have objectively correct
  fixes and waiting on taste decisions just delays them.
- Restate the direction in three lines — palette move, type/layout move, motion move — before
  writing anything, so the user can stop you cheaply.

## Phase 3 — Apply, in strict order

Tokens first, components second, motion last. Each layer lands on the stable ground of the one
before it. Work one cluster at a time; a cluster is one coherent group of changes you can
verify and revert together.

1. **Tokens** — palette, spacing scale, radii, shadows, type scale. Change the system, not the
   instances; instances inherit the fix for free.
2. **Components** — interaction states, hierarchy, layout. Every interactive element leaves
   this phase with visible `:hover`, `:focus-visible`, and `:active` states; loading, empty,
   and error states get designed, not defaulted.
3. **Motion** — transitions on every state change (hover-tier durations 120–300ms), ease-out
   entrances, microinteractions where the findings called for them. Everything decorative goes
   behind a `prefers-reduced-motion` guard.

Throughout: match the codebase's existing idiom — Tailwind stays Tailwind, CSS modules stay CSS
modules, tokens go where the project already keeps them. A redesign written in a foreign idiom
gets reverted no matter how it looks.

## Phase 4 — Verify each cluster before the next

1. Re-render and re-screenshot the affected surfaces at the same breakpoints and themes as the
   baseline.
2. Re-measure the exact numbers the findings named — hover coverage, the contrast pairs, the
   spacing histogram, whatever the baseline recorded. "Looks better" is not a measurement.
3. **Any category that regresses stops the line.** Revise or revert that cluster before moving
   on — a redesign that trades a layout win for a contrast loss is not converging.
4. Re-run the project's own checks: build, typecheck, lint, and the test suite if it's fast.
   **A redesign that breaks the build is a revert, not a result.**

## Phase 5 — Prove

The contact sheet is the deliverable; the prose narrates it, never replaces it.

1. **Before/after contact sheet** — side-by-side pairs per route per breakpoint per theme, from
   the Phase 1 set against the final render. List the file paths so the user can open them.
2. **Score deltas** — every number from Phase 1, before → after, per category. Flat or negative
   numbers get reported exactly as they came out.
3. **Files touched** — every file, one line each on why.
4. **Deliberately not changed** — what you left alone and why (out of scope, needs a product
   decision, would require a dependency).

**If the after looks worse in any state, say so and iterate or revert.** Never present a
regression as a win — one honest "the dark-mode header got muddier, reverted that cluster" is
worth more than a clean-looking report the user later disproves with their own eyes.

## Recommended next step

Close by printing one line — `→ Recommended next: …` — chosen by what happened:

- Scores converged and the sheet looks right → `/designmaxxing:scan` to confirm the full report
  card moved.
- Interactions still feel flat → `/designmaxxing:motion` for the dedicated pass.
- Token drift kept fighting you → `/designmaxxing:retheme` to rationalize the system properly.
- The direction itself felt wrong mid-flight → `/designmaxxing:brief` for a counter-proposal
  before iterating further.
