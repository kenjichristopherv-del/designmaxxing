---
description: Animation quality audit — easing, durations, choreography, and jank sources, judged from real frames rather than source alone.
argument-hint: "[url or interaction to audit, e.g. 'http://localhost:3000 — the sidebar toggle and modal']"
allowed-tools: Read, Grep, Glob, Bash, TodoWrite, mcp__playwright
---

# Motion audit

Judge the motion this UI already has: is it fast, physical, and intentional — or robotic,
sluggish, and janky? Follow the `design-review-method` skill for the finding format and
severity rubric. This command is about *bad* motion; if the problem is *no* motion, that's
`/designmaxxing:life` — check which one you're holding before proceeding, and hand over if the
animation census comes back near zero.

**This is read-only — do not edit any file.** Frames and screenshots go to the scratch
directory, named `route_viewport_state.png`.

Scope: $ARGUMENTS

If no target was given, audit the app's obviously animated interactions: nav/menu toggles,
modals, accordions, hover transitions, page entrances. This command needs a running app and a
browser tool (Playwright MCP or Chrome DevTools MCP). Without one, run Phase 1 only and open
the report stating that timing, smoothness, and every frame judgment are **Not measured**.

## Phase 1 — Read the motion source (channel A)

- Inventory every `transition` and `animation` declaration and every motion-library call
  (framer-motion/`motion`, GSAP, anime.js, Web Animations API). Build a table: element/
  component, property animated, duration, easing, trigger.
- **Jank sources**: grep for transitions and keyframes on layout properties — `top`, `left`,
  `right`, `bottom`, `width`, `height`, `margin`, `padding`. These force layout every frame;
  `transform` and `opacity` are the compositor-friendly pair. Each hit is a finding with the
  `file:line`.
- **Easing smells**: `linear` on UI state changes reads robotic — reserve it for spinners and
  marquees; entrances want ease-out (fast start, gentle landing), exits tolerate ease-in.
  Default `ease` everywhere is a missed decision, LOW.
- **Duration smells**: hover/state transitions belong in 120–300ms; over 500ms on hover is
  sluggish (MED). Larger movements earn longer durations — a full-screen sheet at 150ms
  teleports; a button at 600ms drags. Flag any single duration constant reused for every
  animation regardless of distance: uniform timing is the tell that nobody choreographed.
- `prefers-reduced-motion`: motion exists, so a missing reduced-motion path is HIGH, not
  polish.

## Phase 2 — Capture real frames (channel B)

Source says what was asked for; frames show what ships.

- For each key interaction (open the menu, open a modal, expand an accordion, hover the
  primary button): trigger it with real input, then capture a **frame burst** — screenshots
  at ~50ms intervals for the first ~800ms.
- Composite the burst into a contact sheet per interaction and judge it: does the element
  move smoothly through intermediate positions, or jump between two frames? Do entering
  elements fade/settle, or pop? Does anything visibly stutter mid-flight?
- Where you need numbers: sample `requestAnimationFrame` gaps during the interaction with
  CPU throttling at 4× — repeated gaps over ~32ms are dropped frames, and the animated
  property list from Phase 1 usually names the culprit.
- `document.getAnimations({subtree: true})` mid-interaction confirms what's actually running
  and its `playbackRate`, `duration`, and `easing` as the engine sees them.

## Phase 3 — Microinteractions and choreography

The small stuff is where motion quality is actually felt:

- **Accordions/disclosures**: expand one — does the height animate, or jump instantly? An
  instant jump on an otherwise-animated page is inconsistency (MED).
- **Toggles/switches/checkboxes**: does the thumb slide and the track cross-fade, or does it
  teleport between states?
- **Entrance choreography**: reload the page and capture the first ~900ms. Everything
  popping in at once on a marketing page is a missed moment (POLISH); staggered reveals that
  take seconds to finish are worse than none (MED). Dashboards get a pass — utility first.
- **Exit symmetry**: modals and menus that animate open but vanish instantly on close (LOW —
  exits should be faster than entrances, not absent).
- **Choreographed vs scattered**: many tiny unrelated animations read as noise; one or two
  orchestrated moments read as identity. This judgment is **Taste** — label it.

## Phase 4 — Modern opportunities (POLISH, never inflate)

Only after the defects: note absent-but-cheap upgrades as POLISH. View Transitions
(`@view-transition` / `startViewTransition`) for cross-page continuity; scroll-driven
animations (`animation-timeline: scroll()`) for marketing reveals; `@starting-style` for
entry transitions without JS. These are opportunities, not findings — a UI with no scroll
theatrics is not broken.

## Phase 5 — Report

Use the skill's finding format, most severe first: jank and reduced-motion first, then
easing/duration, then choreography, then POLISH opportunities. Attach the contact sheets and
the Phase 1 inventory table as evidence. Close with **Scores** (the motion-relevant slice of
the Life Score if computed), **Checked and clean**, and **Not measured**.

Then stop. Do not begin fixing. Offer `/designmaxxing:redesign` scoped to motion.

## Recommended next step

Close by printing one line — `→ Recommended next: …` — chosen by what you found:

- Jank confirmed (layout-property animations, dropped frames) → `/designmaxxing:redesign`
  scoped to motion — the transform/opacity rewrite is mechanical and high-yield.
- Motion is smooth but uniform and characterless → `/designmaxxing:brief` to define a motion
  identity before touching timings one by one.
- Census came back near zero → `/designmaxxing:life`; the problem is absence, not quality.
- Missing reduced-motion path → make it the first item in `/designmaxxing:redesign`.
- Motion is genuinely good → say so, and name the single View Transition or scroll moment
  that would add the most identity per line of code.
