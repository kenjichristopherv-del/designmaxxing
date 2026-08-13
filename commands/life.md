---
description: Dead-UI audit — measure hover coverage, transition presence, and feedback states, then report the Life Score with per-element evidence.
argument-hint: "[url or route to audit, e.g. 'http://localhost:3000/dashboard']"
allowed-tools: Read, Grep, Glob, Bash, TodoWrite, mcp__playwright
---

# Life audit

Answer one question with numbers: does this interface respond when a person touches it, or does
it just sit there? Follow the `design-review-method` skill for the finding format and severity
rubric. "Feels dead" is the single most common complaint about AI-built UIs, and it is
completely measurable — nothing in this command requires taste.

**This is read-only — do not edit any file.** Screenshots go to the scratch directory, named
`route_viewport_state.png`.

Scope: $ARGUMENTS

If no target was given, audit the app's key interactive routes. This command needs a running
app and a browser tool (Playwright MCP or Chrome DevTools MCP). Without one, run the static
half only — Phase 1 and the source sides of Phases 3–4 — and open the report stating that
hover coverage, transition truth, and every timing measurement are **Not measured**.

## Phase 1 — Census the source (channel A)

Fast, browser-free, and already diagnostic:

- Count `@keyframes` rules, `transition`/`animation` declarations, and motion libraries
  (framer-motion/`motion`, GSAP, anime.js, auto-animate) in the dependency tree. Zero across
  all of them means the UI is statically incapable of motion — note it and keep going; the
  runtime pass turns "incapable" into per-element evidence.
- Grep for `:hover`, `:focus-visible`, `:active` in stylesheets and Tailwind `hover:` /
  `focus-visible:` / `active:` variants in markup.
- Check for `prefers-reduced-motion` handling — only relevant if motion exists; its absence
  *with* motion is a HIGH accessibility finding, not polish.
- Find loading/empty/error branches: Suspense fallbacks, skeleton components, `aria-busy`,
  `items.length === 0` branches, error boundaries, disabled-while-pending submit buttons.

## Phase 2 — Measure state coverage (channel B)

The CSSOM scan. Do not try `getComputedStyle(el, ':hover')` — pseudo-class introspection is
an unimplemented proposal and it silently returns the base style. Instead:

1. Walk `document.styleSheets` → every `CSSStyleRule` whose selector contains `:hover`,
   `:focus-visible`, `:active`, or `:focus-within`. Cross-origin sheets throw on `.cssRules`
   — catch, and report those sheets as unscanned rather than silently skipping.
2. Collect the interactive population: `a, button, [role=button], input, select, textarea,
   [tabindex], [onclick]` plus anything with a JS click handler you can detect.
3. For each, test `el.matches(selectorWithPseudoStripped)` against each collected rule.
4. Report **coverage per pseudo-class**: `% of interactive elements matched by any :hover
   rule`, same for `:focus-visible` and `:active`. Under 50% hover coverage is a dead UI
   (HIGH); under 80% `:focus-visible` coverage is an accessibility finding; `:active`
   coverage near zero means no pressed feedback (MED).

## Phase 3 — Prove it with real input (channel B)

Coverage says a rule exists; this proves the element actually responds. Playwright input is
trusted, so `page.hover()` genuinely applies `:hover`:

- Sample 10–20 interactive elements across kinds (primary button, link, card, nav item, input).
- For each: snapshot computed `background-color, color, transform, box-shadow, border-color,
  filter, outline` → `hover()` → read again at ~16ms and ~350ms → diff. The 16ms read
  distinguishes a transition (values mid-flight) from an instant jump (already final).
- **Zero changed properties at 350ms = no hover feedback** — screenshot-crop the element to
  catch JS-driven hover before filing it.
- Repeat with keyboard focus (`Tab` to it — is there any visible change? screenshot each
  stop) and with `mouse.down()` held (pressed state).
- **Transition census** across the whole interactive population: `transitionDuration` of
  `"0s"` on hover-styled elements = instant state jumps, the most reliable "feels dead"
  metric there is. Healthy median is 120–300ms; over 500ms on hover reads sluggish — that's
  a finding too. `transitionProperty: all` everywhere = lazy, and a jank risk; note it.

## Phase 4 — Feedback states (channels B + C)

Life is mostly about the moments between stable states:

- **Loading**: throttle the network (CDP emulation), navigate, screenshot at 500ms and
  1500ms. Blank white = no loading design (HIGH). Generic centered spinner = MED. A
  content-shaped skeleton = pass.
- **Validation**: submit a form with invalid input, screenshot. Browser-default bubbles only
  = no designed validation (HIGH). Check for `aria-invalid` plus an inline message.
- **Acknowledgment**: click a primary action and screenshot at +100ms. No visible change
  within ~100ms violates the Doherty threshold (the feedback window inside which an
  interface feels instant) — MED, HIGH if the action fires a network request with no pending
  state at all.
- **Animation census**: `document.getAnimations({subtree: true})` at load and after
  interactions; count running animations. Combined with Phase 1's static census this settles
  whether motion exists anywhere.
- **Reduced motion**: emulate `prefers-reduced-motion: reduce` and re-run the census —
  decorative motion should drop to ~zero. Only a finding when motion exists.

## Phase 5 — Score and report

Compute the **Life Score (0–100)**: hover coverage 25 · transition coverage 25 ·
loading/empty/error design 20 · focus states 10 · animation census 10 · reduced-motion
respect 5 · view-transition/scroll extras 5.

Report in the skill's finding format, most severe first, and include the evidence tables the
score was computed from: per-pseudo-class coverage percentages, the transition census
(population, % nonzero, median duration), and the sampled hover/focus/active diff table
(element, properties changed, duration observed, screenshot path). Close with **Scores**,
**Checked and clean**, and **Not measured**.

Then stop. Do not begin fixing. Offer `/designmaxxing:redesign` scoped to states and motion.

## Recommended next step

Close by printing one line — `→ Recommended next: …` — chosen by what you found:

- Life Score under 50 → `/designmaxxing:redesign` scoped to hover/focus/transition states
  first; they're the cheapest points on the board.
- States exist but feel wrong (robotic easing, sluggish durations) → `/designmaxxing:motion`
  — that's a quality problem, not a presence problem.
- Loading/empty/error states missing entirely → `/designmaxxing:states` for the full
  inventory before redesigning.
- Motion exists but no `prefers-reduced-motion` path → flag it as the first fix in
  `/designmaxxing:redesign`; it's the accessibility half of this score.
- Score healthy → say so, and name the one POLISH extra (a view transition, a scroll reveal)
  that would add identity — or `/designmaxxing:slop` if the UI is alive but generic.
