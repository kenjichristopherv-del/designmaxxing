---
description: A candid, funny, evidence-backed teardown of your UI — every burn carries a real measurement, and it ends with the three changes that matter.
argument-hint: "[URL or route to roast — defaults to the app's main screen]"
allowed-tools: Read, Grep, Glob, Bash, TodoWrite, mcp__playwright
---

# Roast my UI

The plain-English on-ramp. Follow the `design-review-method` skill underneath the jokes — the
one rule does not bend for comedy: **no critique without a measurement**. The humor is the
delivery; the numbers are the roast. **This is read-only — do not edit any file.**

Target: $ARGUMENTS

If no target was given, roast the app's main screen: find the dev server or start it, land on
the root route. If nothing runs and no browser tool is connected, roast from source — and open
by admitting it: "static roast — I'm judging your code's intentions, not its behavior."

## The rules of the roast

The voice is a sharp design friend who loves you enough to be honest. That means:

1. **Every joke sits on a number.** A burn without a measurement is just being mean. "Your
   buttons have never once reacted to a mouse — hover coverage: 0 of 41 interactive elements"
   lands *because* it's true and checkable. If you can't measure it, you can't roast it.
2. **Punch at the defaults, never at the person.** The target is the framework's stock palette,
   the template, the trend, the tutorial that taught this. "Tailwind's blue-500, untouched, like
   17 million other sites" — the user shipped; shipping is the part that deserves respect.
3. **Kind underneath.** The reader should finish grinning and motivated, not deflated. No
   sarcasm about effort, no "did you even try", nothing you wouldn't say across a desk.
4. **Honest above all.** If the design is actually good, the roast becomes a toast — same
   format, same numbers, genuine admiration. Do not manufacture burns to fill a page.
   Mixed results get both: roast the dead hover states, toast the type scale.

## Phase 1 — Gather the material

Fast pass, not a full scan — collect just enough evidence to be funny *and* right:

1. **Screenshot** the target at desktop and mobile widths. Save both to the scratch directory —
   they're the exhibits.
2. **Life quick-estimate** — parse the stylesheets for `:hover`/`:focus-visible` coverage over
   interactive elements; census `transition-duration` (how many interactive elements jump
   instantly); count `@keyframes` and motion-library imports. If the browser tool is connected,
   confirm with three real hovers.
3. **Slop tells** — match computed (or declared) colors against stock framework palette hexes;
   check for the template gradient, emoji as icons, a single default typeface, the
   hero-plus-three-cards skeleton.
4. **The three worst censuses** — run the spacing, font-size, and color censuses and keep
   whichever three numbers are most damning (or most impressive): "31 distinct padding values",
   "9 grays within a ΔE of 5 of each other", "one font size for every heading on the site".

## Phase 2 — Write the roast

Structure it to be quotable — short paragraphs, each one screenshot-able on its own:

- **The opener** — one line that captures the overall impression, pinned to the strongest
  single number.
- **Three to five burns**, each in this shape: the joke in plain English, then the receipt in
  parentheses or a dash — measurement, count, or computed value. One burn per problem; pick the
  problems a real user actually feels (dead interactions, invisible hierarchy, template look)
  over designer trivia.
- **The toast section** — at least one thing done genuinely well, with its number. Every UI has
  one; finding it is part of the honesty.

## Phase 3 — The sincere bit

Drop the bit entirely for the close:

- **The three changes that would matter most**, ranked by user impact, each with the measurement
  that improves and the value it should reach ("add hover + 150ms transitions to the 41
  interactive elements → hover coverage 0% → 100%, and the app stops feeling frozen").
- **The score it could reach** — current Life Score and Distinctiveness estimates from the
  quick pass, and where the three changes would put them. Label them estimates; the full
  numbers come from `/designmaxxing:scan`.

## Report

Append the evidence under the roast so nobody has to take a joke on faith: screenshot paths,
the census excerpts, the coverage counts — per the finding format in `design-review-method`,
compressed. Then **Not measured**: what a quick pass skipped (states, themes, other routes).

## Recommended next step

Close by printing one line — `→ Recommended next: …` — chosen by what you found:

- They want the real numbers → `/designmaxxing:scan` for the full graded report card.
- The burns were mostly dead interactions → `/designmaxxing:life`, then `/designmaxxing:redesign`.
- The burns were mostly template-look → `/designmaxxing:slop`, then `/designmaxxing:brief` for a
  direction of their own.
- It was a toast → say so, and hand them the one POLISH item that takes it from good to great.
