# The designmaxxing prompt catalog

Copy-paste design review prompts for Claude Code, Cursor, Copilot, ChatGPT, or any coding
assistant.

The slash commands in this plugin are the polished versions of these. This file is for when you
want the raw prompt — to paste somewhere else, to edit for your stack, or to read so you know
what the commands are actually asking for. Prompts that drive a browser degrade honestly: if
your assistant can't render the app, it runs the static half and says exactly what went
unmeasured.

**Contents**

[0. The preamble](#0-the-preamble) · [1. The full scan](#1-the-full-scan) ·
[2. Roast my UI](#2-roast-my-ui) · [3. The redesign brief](#3-the-redesign-brief) ·
[4. The redesign](#4-the-redesign) · [5. Retheme](#5-retheme) ·
[6. Diff review](#6-diff-review) · [7. The life audit](#7-the-life-audit) ·
[8. Motion](#8-motion) · [9. Slop & distinctiveness](#9-slop--distinctiveness) ·
[10. Layout & hierarchy](#10-layout--hierarchy) · [11. Typography](#11-typography) ·
[12. Color](#12-color) · [13. States](#13-states) · [14. Responsiveness](#14-responsiveness) ·
[15. Tokens & drift](#15-tokens--drift) · [16. Learning](#16-learning) ·
[17. Rules while coding](#17-rules-while-coding)

---

## 0. The preamble

**Paste this before any design prompt.** It is the single highest-leverage thing in this file.
Without it you get a mood board of plausible opinions — "consider improving the visual
hierarchy" — that changes nothing. With it you get findings you can act on and verify.

```
Before reporting any design finding, you must satisfy all of these:

1. No critique without a measurement. Every finding names the thing (a selector, a
   component, a file:line), the number you measured (computed value, count, coverage %,
   contrast ratio), and what the number should be. "The spacing feels inconsistent" is a
   mood; "27 distinct padding values, 41% off the 4px grid, worst offenders .card (13px)
   and .nav (17px)" is a finding.
2. Render before you judge, if you can. Screenshot the running app before reading source —
   source shows intent, the render shows what users get, and they diverge constantly. If
   you cannot render, say so up front and mark every render-dependent claim "Not measured".
3. Judge computed styles, not class names. text-gray-500 is what was asked for;
   getComputedStyle is what happened after the cascade and the overrides had their say.
4. Judgment that can't be measured — hierarchy, balance, personality — is allowed, but
   label it [Taste] so the reader knows which claims are checkable.
5. Say where each finding holds: route, viewport, theme, state. A finding that only exists
   in dark mode is a dark-mode finding.
6. Count the population, not the first example. "Buttons are inconsistent" needs the
   census: how many style signatures, out of how many buttons.
7. Severity ladder: BLOCKER (broken or exclusionary — failed WCAG 2.2 AA contrast,
   page-level overflow, invisible focus) > HIGH (clearly hurts usability or credibility) >
   MED (noticeably unpolished) > LOW (a designer would catch it) > POLISH (taste-level).
   Never inflate POLISH to MED to look substantial. One BLOCKER outweighs any polish.
8. Zero findings is a valid result. If it's clean, say it's clean and list what you checked.

Report each finding as:
[SEVERITY] Title
In plain English: one sentence a non-designer could follow
Where: selector/component + file:line | Surface: route, viewport, theme, state
Measured: the number vs what it should be
Evidence: screenshot path, quoted computed style, or census excerpt
Fix: the specific change, as code or an exact value move
Verify: what to re-measure afterward and the number that should come back

End with three sections: Scores (if you computed any), Checked and clean (what you measured
and found healthy), and Not measured (channels that didn't run, routes not visited, states
not reached — never round this to zero).
```

---

## 1. The full scan

**1.1 — The full design audit** *(the flagship — start here)*
```
Audit this app's frontend design end to end and grade it. Work in passes:

RECON: detect the stack (framework, styling system, token files, component library),
enumerate the routes, and pick an audit set: home/landing, one dense screen, one form, one
detail page. Say whether you're grading a marketing surface or a dashboard — density norms
and scroll-life expectations differ.

STATIC PASS (no browser needed): raw hex/px values outside the token files and the
token-vs-hardcoded ratio; interactive components with no :hover/:focus-visible styles
anywhere in source; missing loading/empty/error branches; zero @keyframes + zero
transitions + no motion library (a statically dead UI); transition: all (lazy); no
prefers-reduced-motion query despite motion; stock framework palette hexes; template
gradients; emoji in headings/buttons; 100vh without a dvh fallback; hover-only disclosure
with no touch path; missing srcset on large images.

RUNTIME PASS (if you can drive a browser): per route, census the computed styles —
spacing histogram (every computed margin/padding/gap; % on the 4px grid; distinct count:
6–12 is a system, 30+ is no system), font sizes fitted against modular ratios
(1.125–1.618), color census in OKLCH (distinct values, hue families, near-duplicate pairs
under a just-noticeable difference), radius census, shadow census, button style-signature
census (more than 3–4 signatures for one semantic tier is a system failure). Measure
hover-state coverage by parsing document.styleSheets for :hover/:focus-visible/:active
rules and matching the stripped selectors against interactive elements — note that
getComputedStyle(el, ':hover') does NOT work, and cross-origin stylesheets throw on
cssRules (report those as unscanned). Census transitionDuration on interactive elements —
"0s" on hover-styled elements means instant state jumps, the most reliable "feels dead"
metric. Sweep for horizontal overflow at 360/768/1024/1440 — page-level scrollWidth >
clientWidth is a BLOCKER.

TOOLING PASS: run Lighthouse and axe-core if available rather than reimplementing them.
WCAG 2.2 AA is the compliance bar (4.5:1 body text, 3:1 large text and UI components); if
you also run APCA, label it "perceptual contrast — advisory", never compliance.

VISION PASS: screenshot the audit routes at 360/768/1440 (plus dark mode if it exists) and
judge what metrics can't see: is there one primary action above the fold or several
shouting? Does size/weight/color order match importance order? Do edges almost line up?
Does this look like every AI-built page? Every judgment here is either backed by a
measurement or labeled [Taste].

SCORE AND REPORT: score nine categories 0–100 — Life 18, Layout 15, Typography 15, Color
12, Accessibility 12, Responsiveness 10, Consistency 8, Perceived performance 5,
Distinctiveness 5 — weighted mean for the overall, any BLOCKER caps its category at 49,
an accessibility BLOCKER caps the overall at 59. Letter grade: A ≥90, B ≥80, C ≥70,
D ≥55, F below. Then the findings ledger, most severe first, per the preamble format.
Close with a one-page redesign brief: the direction this product should take, argued from
its own domain — not from trends — and the five highest-leverage moves in order.

Do not fix anything. Report only.
```

**1.2 — Quick pass** *(two minutes, no browser)*
```
Static-only design audit of this codebase, no browser: token drift (raw hex/px outside
token files, with counts and worst files), interactive components missing hover/focus
styles in source, missing loading/empty/error branches, motion census (@keyframes,
transitions, motion libraries — zero across all three means the UI cannot move), stock
framework palette values, template gradients, emoji as icons, and responsive smells (100vh
without dvh, hover-only reveals). Report per the preamble, and open by stating plainly
that nothing was rendered: hover coverage, computed censuses, and every visual judgment
are Not measured.
```

---

## 2. Roast my UI

**2.1 — The roast**
```
Roast this UI. The rules: every joke must sit on a real measurement — a burn without a
number is just being mean; punch at the defaults, the framework, and the trend, never at
me; kind underneath — I should finish grinning and motivated; and honest above all — if
the design is actually good, the roast becomes a toast with the same numbers.

Gather the material first (quick pass, not a full audit): screenshot desktop and mobile if
you can; estimate hover coverage and count how many interactive elements change nothing on
hover; census transition durations (how many elements jump instantly); count @keyframes
and motion libraries; match the palette against stock framework hexes; check for the
template gradient, emoji as icons, a single default typeface, the hero-plus-three-cards
skeleton; run the spacing, font-size, and color censuses and keep the three most damning
numbers.

Then write it: an opener pinned to the strongest single number; three to five burns, each
one a plain-English joke followed by its receipt ("your buttons have never once reacted to
a mouse — hover coverage: 0 of 41 interactive elements"); a toast section — at least one
thing done genuinely well, with its number.

Then drop the bit: the three changes that would matter most, ranked by user impact, each
with the measurement it improves and the value it should reach, plus an estimate of where
the scores would land. Append the evidence under the roast — screenshots, censuses,
counts — so nobody has to take a joke on faith.
```

---

## 3. The redesign brief

**3.1 — Direction before pixels**
```
Produce a redesign brief for this app — direction only, change no code.

First extract the project's intent: read the existing tokens and theme files, the brand
assets, the README/docs for how the team talks about the product, and find the best screen
in the app — the one that most looks like someone cared — and name what it does right.
Characterize the product in one sentence: who uses it, in what setting, and what feeling
the interface should produce. Everything downstream must justify itself against that
sentence.

Then propose TWO genuinely different directions (a primary and a counter-proposal, not a
strong option and a straw man). For each, specify concretely enough that an implementer
has no taste decisions left open:
- Palette: 4–6 named hexes with roles (ground, surface, ink, accent, semantics), neutrals
  hue-tinted toward the accent, core text/ground pairs confirmed against WCAG 2.2 AA.
- Type pairing: display, body, and data faces with full fallback stacks, weights, and the
  scale ratio — and why each face fits the personality sentence ("it's clean" is not a why).
- Space, radius, shadow language: base unit and scale, the 2–4 radii, the elevation story.
- Motion identity: what animates and what never does, signature duration and easing, the
  entrance story, the hover grammar.
- Layout moves: the 3–5 structural changes, named per screen.
- What to deliberately keep: the things already working.

Stock framework palettes, purple-to-blue gradients, emoji as icons, and a lone default
typeface are forbidden as proposals unless I explicitly asked for them.

Recommend one direction in two sentences, tied to the personality sentence. End with the
execution scope, phased: tokens first, components second, motion last. Then stop — I pick.
```

---

## 4. The redesign

**4.1 — Apply, verify, prove**
```
Execute this design direction in the codebase. Presentation code only — styles, tokens,
markup structure, class lists, motion. Do not touch application logic, data fetching, or
routing behavior, and do not introduce a new styling system or dependency without asking.

BASELINE FIRST — no baseline, no redesign. Screenshot the affected routes at 360, 768,
and 1440 minimum, in both themes if the app has them, and save them as the "before" set.
Record the current numbers you intend to move (hover coverage %, failing contrast pairs,
spacing histogram, distinct font sizes). If you cannot render the app, stop and tell me:
proceeding means the proof is code diffs instead of rendered before/afters — a materially
weaker guarantee — and you need my explicit go-ahead.

Resolve ONE coherent direction (my words, or the brief above). If the findings are
aesthetic and no direction exists, stop and ask for one — redesigning toward your own
defaults is the exact failure this process exists to prevent. Exception: BLOCKER/HIGH
mechanical fixes (failed contrast, page overflow, invisible focus, dead hover states)
proceed without a brief; they have objectively correct fixes. Restate the direction in
three lines before writing anything so I can stop you cheaply.

APPLY IN STRICT ORDER, one revertible cluster at a time:
1. Tokens — palette, spacing scale, radii, shadows, type scale. Change the system, not
   the instances.
2. Components — every interactive element leaves this phase with visible :hover,
   :focus-visible, and :active states; loading, empty, and error get designed.
3. Motion — transitions on every state change (120–300ms hover-tier, ease-out entrances),
   everything decorative behind a prefers-reduced-motion guard.
Match the codebase's existing idiom throughout — Tailwind stays Tailwind, CSS modules
stay CSS modules.

VERIFY EACH CLUSTER before the next: re-render, re-screenshot, re-measure the exact
numbers from the baseline. Any category that regresses stops the line — revise or revert
that cluster. Re-run the project's build, typecheck, and lint; a redesign that breaks the
build is a revert, not a result.

PROVE: before/after screenshot pairs per route per breakpoint per theme; every baseline
number, before → after, reported exactly as it came out even if flat or negative; every
file touched with one line on why; what you deliberately did not change and why. If the
after looks worse in any state, say so and iterate or revert — never present a regression
as a win.
```

---

## 5. Retheme

**5.1 — Rationalize the tokens and migrate everything**
```
Rationalize this codebase's design tokens and migrate every usage. This is a refactor
with a visual invariant: the app should look the same after, except where the approved
mapping says otherwise.

EXTRACT first — no census, no retheme. Per dimension (colors, spacing, radii, shadows,
type sizes): every literal value in use, its usage count, and its file:line sites; which
values already flow through tokens vs raw literals; where the project keeps tokens today.
Call out near-duplicates explicitly — #f4f4f5 and #f5f5f5 both in use is the disease
being treated.

RATIONALIZE on paper before touching a file: map every de-facto value to its nearest
sanctioned token and present the full mapping table — old value → new token → visual
delta, with usage counts. Flag every mapping where the change is perceptible (a visible
color difference, or more than 2px of spacing) — those are design decisions, not
cleanups. Name tokens semantically (--surface, --text-primary, --space-4), never by
appearance (--gray-3). Put the system where the project already keeps one. Anything that
resists mapping — a hex that could go two ways, a value that looks intentional — goes on
an "asks" list, not into a guess. SHOW ME THE TABLE AND STOP. Migrate nothing until I
approve it.

Then: screenshot the affected routes as a baseline; land the new token definitions
alongside the old values; migrate usages batch-by-batch (per directory or component
family), preferring exact-literal greps, counting replacements against the census and
accounting for any gap; run build and typecheck after every batch; remove the orphaned
old definitions at the end.

PROVE: re-screenshot and diff against the baseline — target is near-zero visual change
except the approved deltas; report any drift honestly with the screenshot pair, then fix
or revert it. Lead the report with the numbers: 14 grays → 4, 27 paddings → 8, plus the
literal-vs-token ratio before and after, every file touched, and the asks list still open.
```

---

## 6. Diff review

**6.1 — Review only what changed**
```
Design-review my uncommitted changes (or the branch/PR I name). Review ONLY what this
diff introduces or worsens — pre-existing problems nearby get one line at the end, no
severities.

Resolve the changed files and keep the ones with design surface: components, templates,
stylesheets, class lists, token/theme files, fonts, images. Rank by blast radius (a token
file touches every screen; a shared component touches many; a page file touches one).
Read each hunk with its surrounding file and grep the project's tokens first, so you know
what "on-system" means here.

Check, with measurements: new hardcoded values where tokens exist for exactly that value
(quote the token it should have used); new interactive elements shipped dead — no :hover,
no :focus-visible, no transition, no disabled/loading treatment (a component born without
states stays dead for years); new data views missing loading/empty/error branches; new
forms with only browser-default validation; spacing or font sizes that miss the repo's
existing scale (list the scale, then the miss); contrast of every NEW text/background
pair against WCAG 2.2 AA, in both themes if the repo has them; slop tells introduced
(stock palette hexes, template gradients, emoji as icons); accessibility of new markup
(click handlers on bare divs, missing alt/labels, outline: none without replacement,
tabindex > 0); new animation without a reduced-motion path, or transitions on layout
properties (top/width) instead of transform/opacity.

If the app runs and you can render it, screenshot only the affected routes and test the
new interactive elements with a real hover and a real Tab stop.

A clean diff gets told so, specifically — "4 new components, all with states; no new raw
values; both new color pairs pass AA." Praise is evidence too.
```

---

## 7. The life audit

**7.1 — Does this UI respond, or just sit there?**
```
Measure whether this interface responds when a person touches it. Nothing here requires
taste — report numbers.

STATIC CENSUS: count @keyframes rules, transition/animation declarations, and motion
libraries in the dependency tree (zero across all = the UI is statically incapable of
motion); grep for :hover/:focus-visible/:active and their Tailwind variants; check for
prefers-reduced-motion handling (its absence WITH motion present is a HIGH accessibility
finding, not polish); find loading/empty/error branches in source (Suspense fallbacks,
skeletons, aria-busy, zero-length conditionals, error boundaries, disabled-while-pending
buttons).

STATE COVERAGE (if you can drive a browser): do NOT use getComputedStyle(el, ':hover') —
pseudo-class introspection doesn't exist; it silently returns the base style. Instead:
walk document.styleSheets for every rule whose selector contains :hover, :focus-visible,
:active, or :focus-within (cross-origin sheets throw on .cssRules — catch and report them
as unscanned); collect the interactive population (a, button, [role=button], inputs,
[tabindex], [onclick]); test el.matches(selectorWithPseudoStripped) for each. Report
coverage per pseudo-class. Under 50% hover coverage is a dead UI (HIGH); under 80%
focus-visible coverage is an accessibility finding; near-zero :active means no pressed
feedback (MED).

PROVE WITH REAL INPUT: sample 10–20 interactive elements across kinds; snapshot computed
background/color/transform/box-shadow/border/filter/outline → hover → read again at ~16ms
and ~350ms → diff (the 16ms read distinguishes a transition mid-flight from an instant
jump). Zero changed properties at 350ms = no hover feedback — screenshot-crop the element
to catch JS-driven hover before filing. Repeat with keyboard focus and with mouse-down
held. Census transitionDuration across the whole interactive population: "0s" on
hover-styled elements = instant state jumps, the most reliable "feels dead" metric there
is; healthy median 120–300ms; over 500ms reads sluggish — that's a finding too.

FEEDBACK STATES: throttle the network, navigate, screenshot at 500ms and 1500ms — blank
white = no loading design (HIGH), generic spinner = MED, content-shaped skeleton = pass.
Submit a form with invalid input — browser-default bubbles only = no designed validation.
Click a primary action and screenshot at +100ms — no visible change violates the Doherty
threshold (the window inside which an interface feels instant). Run
document.getAnimations({subtree: true}) at load and after interactions. Emulate
prefers-reduced-motion: reduce and confirm decorative motion drops to ~zero.

SCORE — the Life Score (0–100): hover coverage 25 · transition coverage 25 ·
loading/empty/error design 20 · focus states 10 · animation census 10 · reduced-motion
respect 5 · view-transition/scroll extras 5. Include the evidence tables the score came
from: per-pseudo-class coverage, the transition census (population, % nonzero, median
duration), and the sampled hover/focus/active diff table.
```

---

## 8. Motion

**8.1 — Is the motion good, or just present?**
```
Audit the quality of this UI's motion — easing, durations, choreography, jank. (If the
animation census comes back near zero, stop: the problem is absence, not quality — run
the life audit instead.)

READ THE SOURCE: inventory every transition, animation, and motion-library call into a
table (element, property, duration, easing, trigger). Jank sources: transitions or
keyframes on layout properties — top, left, width, height, margin, padding — force
layout every frame; transform and opacity are the compositor-friendly pair; each hit is a
finding at its file:line. Easing smells: linear on UI state changes reads robotic
(reserve it for spinners); entrances want ease-out; unexamined default ease everywhere is
a missed decision. Duration smells: hover/state transitions belong in 120–300ms; over
500ms on hover is sluggish; a full-screen sheet at 150ms teleports; one duration constant
reused for everything regardless of distance is the tell that nobody choreographed.
Motion exists, so a missing prefers-reduced-motion path is HIGH, not polish.

CAPTURE REAL FRAMES (if you can drive a browser): for each key interaction (menu, modal,
accordion, primary-button hover), trigger it with real input and capture a frame burst —
screenshots every ~50ms for the first ~800ms — and composite a contact sheet: does the
element move through intermediate positions or jump between two frames? Sample
requestAnimationFrame gaps with 4× CPU throttle — repeated gaps over ~32ms are dropped
frames, and the layout-property list usually names the culprit. Read
document.getAnimations() mid-flight for the durations and easings the engine actually
runs.

MICROINTERACTIONS: accordions that jump instead of animating height (on an
otherwise-animated page, that inconsistency is MED); toggles whose thumb teleports; page
entrance — everything popping in at once on a marketing page is a missed moment, but
staggered reveals that take seconds are worse than none; modals that animate open but
vanish instantly (exits should be faster than entrances, not absent). Whether the motion
reads as one identity or scattered noise is [Taste] — label it.

Note absent-but-cheap upgrades (View Transitions, scroll-driven animations,
@starting-style) as POLISH opportunities only, after the defects. A UI with no scroll
theatrics is not broken.
```

---

## 9. Slop & distinctiveness

**9.1 — How templated does this look?**
```
Score how much this UI looks like every other AI-built UI. The answer is a count, not an
opinion.

INTENT FIRST: a tell the team chose deliberately is not slop. Read the brand docs, token
files, and design notes before deducting anything; note which tells were excused and why.
My stated direction always wins.

Start at 100 and run the tell census — match in source AND on computed values if you can
render (people copy the Tailwind palette into custom CSS, so class-name greps
under-count):
- Stock Tailwind palette shipped as-is (computed hexes: slate #f8fafc #f1f5f9 #e2e8f0
  #64748b #1e293b #0f172a, blue-500 #3b82f6, indigo/violet/purple #6366f1 #8b5cf6
  #a855f7, pink-500 #ec4899; untouched config with no extend.colors): −15
- Purple→blue or purple→pink hero gradient: −15
- Inter, Poppins, Roboto, or Geist as the only typeface, no display face: −10
- Emoji as icons in headings, feature cards, or buttons (🚀 ✨ 💡): −10
- The canned landing skeleton (hero + exactly three feature cards + logo strip + pricing
  + FAQ, in order): −10
- Colored 3–4px left-border accent strips on cards: −8
- Gradient hero text (background-clip: text): −6
- Uniform card recipe everywhere (white bg + gray 1px border + rounded-xl + shadow-sm on
  100% of cards): −6
- Glassmorphism pill badge with a sparkle over the h1: −5
- Sloganeering copy (Supercharge, Seamless, Unlock, Effortless, 10x, "Built for the
  modern…"): −5

Then add back up to +15 for identity that's actually present: a real display/body pairing
that actually loads, neutrals hue-tinted toward the brand color, bespoke illustration or
photography, a signature motion used consistently, a distinctive radius/border/texture
language applied throughout.

Every deduction gets evidence: the computed value or DOM excerpt, a screenshot crop, and
the file:line. Bands: 85+ distinctive · 60–84 competent but derivative · 35–59
template-adjacent · below 35 slop.

End with the actionable part: what distinctive would look like for THIS product — two or
three directions argued from its actual domain, audience, and existing assets, not from
trends.
```

---

## 10. Layout & hierarchy

**10.1 — System or accumulation of accidents?**
```
Measure whether this UI has a spatial system. If you can drive a browser, run these in
the page; otherwise grep the declared values and say the render checks didn't run.

Spacing histogram: collect every computed margin, padding, and gap in px. Report the
distinct-value count (6–12 is a system; 30+ is no system), the % divisible by 4 (or by
the detected base unit — the mode of pairwise GCDs), and the worst offenders with
selectors, attributed to source with a grep for the raw number.

Alignment clustering: collect getBoundingClientRect().left (and right) for top-level
blocks and cluster the x-coordinates. Edges within 1–8px of a cluster but not on it are
near-misses — the misalignments an eye registers as "off" without knowing why. List each
with both selectors and the pixel delta.

Container discipline: text containers wider than ~1200px, and sibling sections whose
max-widths differ without reason (1140 vs 1200 vs 1280).

Above-the-fold clarity at 1440×900 and 390×844: is there ONE identifiable primary action?
Count elements competing at maximum visual weight (large AND bold AND saturated) — more
than 2 means no hierarchy, and the count is the finding. Confirm the primary CTA is the
highest-contrast element above the fold.

Heading monotonicity: computed font-size must decrease h1 → h4, one h1 per page.

Ink ratio per section (sum of element boxes vs section area): above ~65% reads cramped,
below ~15% reads barren — the barren judgment is [Taste] if the design is deliberately
editorial.

Vertical rhythm: section gaps should come from a small consistent set (64/96/128), and
intra-group spacing must be smaller than inter-group spacing — a caption belongs closer
to its image than to the next block. Compare sibling gaps and quote both numbers.

Component consistency: for each repeated component with ≥3 instances, diff computed
padding, radius, shadow, and gap across instances and report the census (".card: 3
padding variants across 14 instances: 16px ×9, 20px ×4, 13px ×1").

Z-index: a ladder like 10/20/30 is a system; 9999 and 2147483647 are stacking-context
hacks worth a LOW finding.
```

---

## 11. Typography

**11.1 — Judge the type that renders**
```
Audit this app's typography from what actually renders, not what was declared.

CENSUS (if you can drive a browser): walk visible text nodes and collect computed
font-family, font-size, font-weight, line-height, letter-spacing. Text rendering in
Times/Arial when a webfont was declared means the font never loaded — BLOCKER, confirm
via document.fonts. Inter/Poppins/Roboto/Geist as the ONLY face with no display font is a
MED genericness finding. Weight census: only-400 everywhere = flat hierarchy; four
weights in one paragraph = noise; a weight in use that document.fonts never loaded = faux
bold (the browser smearing a fake bold onto a face that doesn't have one).

SCALE: histogram the sizes and fit least-squares against the modular ratios 1.125, 1.2,
1.25, 1.333, 1.414, 1.5, 1.618. Healthy is 5–8 sizes on one ratio; 12+ arbitrary sizes is
no scale — report the list, the best-fit ratio, and its error. h1:body under ~1.5× on a
marketing page is timid; over ~4× on a dense app is shouting. Attribute off-scale sizes
to file:line.

READING COMFORT: body line-height outside 1.4–1.7 (computed lineHeight/fontSize);
headings above 32px with line-height above 1.3; line length outside 45–90ch (estimate
chars per line as rect.width / (fontSize × 0.5)) — over 100ch on prose is HIGH; primary
body copy at opacity < 0.6 or light gray on white is a legibility finding even when it
technically passes contrast — measure and say which failure it is; justify without
hyphens produces rivers (LOW).

REFINEMENT AND LOADING: display text over 40px with default tracking wants −0.01 to
−0.03em; ALL-CAPS labels want positive tracking; absence of text-wrap: balance on
headings and text-wrap: pretty on prose is LOW (one-line fixes whose absence signals no
typographic pass happened). Check font-display strategy, preload links, size-adjust
fallback metrics; screenshot first paint vs after document.fonts.ready — a visible diff
is FOUT (a flash of fallback text before the real font arrives), MED.
```

---

## 12. Color

**12.1 — Census, compliance, craft**
```
Audit this app's color in that order: coherence, compliance, craft.

CENSUS (if you can drive a browser): collect every computed color, background-color, and
border-color; convert to OKLCH (perceptually uniform — distances match what eyes see) and
cluster. A healthy product UI is 1 brand hue + 1–2 accents + one neutral ramp + a
semantic set; 6+ unrelated hue families is incoherence — name them. Near-duplicate pairs
that are visually identical but both in use (#f4f4f5 and #f5f5f5) are token drift — list
each pair with selectors. Match computed hexes against the stock Tailwind sets (slate
#f8fafc #f1f5f9 #e2e8f0 #64748b #1e293b #0f172a, #3b82f6, #6366f1 #8b5cf6 #a855f7,
#ec4899) — exact hits mean shipped defaults.

COMPLIANCE: WCAG 2.2 AA is the bar — 4.5:1 body text, 3:1 large text and non-text UI
components; run axe-core rather than eyeballing. Every failure is a BLOCKER with the
measured ratio, both hexes, and the selector. If you also run APCA, label it "perceptual
contrast — advisory, not compliance" (reference: Lc ≥75 body, ≥60 large, ≥45 large bold)
— it catches thin light text that ratio math wrongly passes, but it is not a standard.

SEMANTICS AND NEUTRALS: red/green/amber as decoration, or errors in the brand color,
breaks learned meaning — MED per misuse; check a semantic token set exists rather than
raw reds sprinkled. #000 body text on #fff is harsh (near-blacks #111–#1a1a1a read
better) — LOW; a "dark mode" that is pure #000 behind pure #fff is the no-design-pass
giveaway — MED. Note whether the grays are hue-tinted toward the brand (chosen) or stock
(inherited) — [Taste].

DARK MODE: emulate prefers-color-scheme: dark and screenshot. Does it exist; is
color-scheme set so form controls follow; naive-inversion checks — shadows that vanish
(elevation must come from lighter surfaces in dark), borders that disappear, glaring
images, elevated surfaces darker than the base (backwards). Re-run contrast in dark — a
pair passing light and failing dark is a dark-mode BLOCKER.

GRADIENTS AND ACCENT: saturated hue pairs crossing the gray dead-zone without a midpoint
go muddy; long two-stop gradients band. Estimate accent coverage — past ~10% of pixels it
stops reading as an accent.
```

---

## 13. States

**13.1 — The screens that only exist when things go wrong**
```
Audit every state a real user meets — waiting, nothing-here-yet, something-went-wrong,
just-clicked — and whether each was designed or merely happens. Reach error and empty
states against dev/staging seeds only, never by submitting real data.

INVENTORY IN SOURCE: loading (skeletons, Suspense fallbacks, aria-busy, isLoading
branches); empty (zero-length conditionals — designed component with explanation and next
action, or a bare string?); error (boundaries, validation branches, aria-invalid); and
the missing-entirely list — a fetch with no pending branch, a list with no empty branch,
a mutation with no error branch. Absence in source is a finding before anything renders.

RENDER THE LOADING STATES (if you can drive a browser): throttle the network, navigate,
screenshot at 500ms and 1500ms. Blank white at 500ms = HIGH. Generic centered spinner
for a >1s load = MED; content-shaped skeletons pass — and diff the skeleton against the
loaded layout: a skeleton shaped differently from the content makes the page load twice.

RENDER EMPTY AND ERROR: reach each empty state (seeded-empty or a filter matching
nothing) — designed means it explains what belongs here and offers the action that fills
it. Submit invalid input — browser-default bubbles = undesigned (MED); designed is
aria-invalid plus an inline message next to the field saying how to fix it. Kill a
request — an error boundary should catch it; a raw stack trace on screen is HIGH.

FEEDBACK TIMING: click each primary action and screenshot at +100ms — no visible change
(no disable, no progress, no optimistic update) is MED; a button that stays clickable
during its own request is a double-submit exposure, HIGH. Tab through each screen,
screenshotting every stop — no visible focus change is a BLOCKER; grep outline: none
without a replacement — each hit is an instant BLOCKER at its file:line. Disabled
controls must look disabled and carry cursor: not-allowed.

THE ENDINGS: users remember the last screen of a flow disproportionately (Peak–End). Walk
each key flow to its end and screenshot it — a designed confirmation states what happened
and what's next; a silent redirect or bare toast on a consequential flow is MED.
```

---

## 14. Responsiveness

**14.1 — Where the layout stops being designed**
```
Audit responsiveness by rendering, not by reading media queries.

SWEEP: screenshot each in-scope route at every ~100px from 320 to 1600, plus 1920.
Review the strip for discontinuities (layout snapping rather than adapting — flag both
widths) and dead zones — ranges where the layout is just a stretched version of the
previous breakpoint (the classic 768–1023 stretched-mobile band). A dead zone means
nobody designed for those widths; tablets live there.

OVERFLOW: at 360, 390, 768, 1024, 1280, 1440 — document.documentElement.scrollWidth >
clientWidth is a page-level BLOCKER; find the culprit element and name it. Tables, pre,
code blocks, and long unbroken strings without an overflow-x: auto container are the
next overflow bug waiting — MED at file:line.

MOBILE at 390×844: the nav — is there a menu control; does opening it animate or
teleport; does it trap focus, close on Esc and backdrop tap, lock body scroll; are its
targets ≥44px? Touch targets under 24×24 CSS px fail WCAG 2.2 SC 2.5.8 — BLOCKER; under
44×44 on mobile is HIGH; adjacent targets closer than 8px get flagged together.
Hover-only tooltips/menus/reveals with no tap path: if content is unreachable on touch,
HIGH — check @media (hover: none) handling. At 320–360: word-broken headings, buttons
wrapping to two lines, orphaned CTAs.

FLUID BEHAVIOR: compare computed h1 at 360 vs 1440 — identical-and-huge or
identical-and-small both mean no fluid strategy (clamp() is the fix), MED on marketing
surfaces, quote both sizes. 100vh without a dvh/svh fallback is the mobile URL-bar bug —
MED per file:line; fixed bottom bars without env(safe-area-inset-*) clip the home
indicator. Set zoom to 200% and re-run the overflow scan — breakage is a WCAG 1.4.4
failure, BLOCKER.

IMAGES: srcset/sizes/picture presence; intrinsic vs rendered size per image — ratio >2×
is shipping wasted bytes, <1× is upscaled and blurry (confirm on the screenshot).
```

---

## 15. Tokens & drift

**15.1 — The declared system vs the one that grew in the dark**
```
Audit this codebase's design-system health: extract the de-facto system from what
renders, diff it against the declared one, measure the drift, and propose the
rationalized system.

DECLARED: collect what the codebase claims — CSS custom properties, the framework
config's extend block, theme files. Note whether semantic names exist (--surface,
--text-primary) or only raw scales. An untouched framework config (no extend.colors, no
extend.fontFamily) is itself a finding — the system was never made theirs.

DE-FACTO (if you can drive a browser): census the computed values across routes — every
color, margin/padding/gap, border-radius, box-shadow, and (font-size, weight,
line-height) tuple. This is the system users actually experience.

DRIFT: every raw hex/px outside the token files with file:line, and the ratio of
token-sourced to hardcoded declarations. Radius census: 2–4 related values is a system;
8+ arbitrary is drift. Shadow census: more than ~5 distinct strings, or shadows with
inconsistent light direction (some up, some down — a physical inconsistency viewers feel
without naming). Near-duplicate colors both in use (the 14-grays problem). Type tuples
matching no member of the detected scale.

COMPONENTS: group every button by its computed (background, padding, radius, font-size,
weight) signature — more than 3–4 signatures for one semantic tier is HIGH; two visually
different PRIMARY buttons on one page is HIGH on its own. Repeat for inputs and links.
Multiple icon libraries, or icon sizes/stroke-widths varying within one toolbar — MED.
Near-duplicate components (two Cards, three Modals) — MED per family with all paths.
Dark mode as scattered per-element overrides instead of token redefinition — name the
count.

PROPOSE the rationalized system as a mapping table ready to execute:
  Grays:  14 in use → 4 proposed   #f4f4f5,#f5f5f5,#fafafa → --surface (#f5f5f6) [41 usages]
  Radii:   7 in use → 3 proposed   3px,4px,5px → --radius-sm (4px)               [23 usages]
Every row: current values, proposed token, usage count, files touched. Flag any merge
that would change a WCAG contrast result — those need a human eye.
```

---

## 16. Learning

**16.1 — Explain it against my code**
```
Explain [the concept or finding] against my actual codebase — teach, don't fix.

Find where it applies and where it's violated in THIS repo, with real file:line
references and quoted values. Find the best local example too, if one exists ("your
Button already does this right — the fix is making Card match it"): a local positive
example teaches faster than any external one. If the concept genuinely doesn't appear
here, say so and use a minimal invented example clearly labeled as such.

Explain in layers: (1) plain English — one or two sentences a non-designer could repeat;
(2) how it works — what the eye or the browser actually does, and which CSS properties
drive it; (3) why it matters HERE — the effect on this product's real user, tied to the
examples; (4) the fix pattern as a short code sketch — the shape, not the applied fix;
(5) how to spot it next time — the one-line heuristic and, where one exists, the
measurement.

Cite the canon when there is one — Laws of UX for perception, WCAG 2.2 for the exact
ratios, MDN for platform mechanics — one or two authoritative sources, verified, not a
bibliography. Where sources disagree (WCAG 2.2 vs APCA contrast math is the classic),
say so and say which is the compliance bar. Never invent a citation, a statistic, or a
"studies show".
```

---

## 17. Rules while coding

These two blocks are the proactive halves of the plugin — paste them into your assistant's
rules/system prompt (CLAUDE.md, .cursorrules, custom instructions) and the audits above stop
finding anything.

**17.1 — The alive-UI contract** *(so the life audit never fires)*
```
When writing or editing any interactive UI, these are requirements, not suggestions:

1. Every interactive element ships with all five states — rest, hover, focus, active,
   disabled — in the same edit that creates it. An element missing a state isn't minimal,
   it's unfinished.
2. Every :hover is paired with a transition: 120–300ms, ease-out, named properties —
   never `transition: all`.
3. :focus-visible gets a branded outline (2px solid accent + outline-offset: 2px). Never
   `outline: none` without a replacement.
4. :active gives pressed feedback (subtle scale or darken). Cursor is honest: pointer on
   everything clickable, not-allowed on disabled.
5. Any user action shows visible feedback within 100ms — disable the button and show
   progress in the same frame the click lands, or update optimistically and reconcile.
   Never a silent await the user can double-click through.
6. Loads over ~1s get a content-shaped skeleton (matching the final layout, so nothing
   jumps), region marked aria-busy. Never a blank region.
7. Empty states explain what belongs here and offer the action that fills it — never bare
   "No items". Form errors are inline, specific, aria-invalid, next to the field. The
   success/confirmation screen is designed — it's the moment the user remembers.
8. Animate transform and opacity only — never width/height/top/left/margin. Every
   decorative animation lives inside @media (prefers-reduced-motion: no-preference) from
   day one.
9. Durations scale with size: hover 120–200ms, small entrances 200–300ms, panels
   300–500ms. One signature easing per project, tokenized.
10. Keyboard is first-class: Tab reaches it, Enter/Space operates it, Esc closes it, focus
    returns to the trigger after a modal closes. onClick on a div means it should have
    been a button.
```

**17.2 — The distinctive-UI contract** *(so the slop audit never fires)*
```
When choosing colors, fonts, spacing, or layout, derive the design from the product's own
world — never from defaults. If I pinned a direction, my words win. Otherwise:

Never write unasked: a purple→blue or purple→pink gradient hero; the stock framework
palette verbatim (#3b82f6, #6366f1, #8b5cf6, untouched slate/zinc grays, untouched
config); Inter/Poppins/Roboto as the only typeface; emoji as icons; the
hero-plus-exactly-three-feature-cards skeleton; gradient headline text; a "✨ Now with AI"
glass pill; colored left-border strips on cards; the uniform white-card-gray-border-
rounded-xl-shadow-sm recipe on everything; copy that says Supercharge, Seamless,
Effortless, Unlock, or 10x.

Instead: answer in one sentence what this product's world is and what someone should feel
in the first two seconds, and derive everything from that. One brand hue used with
conviction. Neutrals tinted 2–5% toward the brand (color-mix does it in one line).
Near-black over #000. A real type pairing — characterful display + workhorse body — or a
deliberately chosen system-stack pairing, on one modular ratio with 5–8 sizes. One base
spacing unit; one radius family of 2–4 values; one shadow language with one light
direction. Semantic tokens (--surface, --text-primary) from day one; dark mode is a token
redefinition, never scattered per-element overrides. Contrast passes WCAG 2.2 AA from the
first draft. One deliberate aesthetic risk per design — and it's the only loud thing on
the page. Buttons say what happens. text-wrap: balance on headings, pretty on prose.
```
