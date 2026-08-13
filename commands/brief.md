---
description: Turn findings into a creative direction — palette, type pairing, motion identity, layout moves — without touching code.
argument-hint: "[the surface and the feeling, e.g. 'the dashboard — calmer, more premium'] (optional)"
allowed-tools: Read, Grep, Glob, Bash, TodoWrite, WebFetch, WebSearch, mcp__playwright
---

# Redesign brief

Decide the direction before anyone edits a file. Follow the `design-review-method` skill for the
evidence discipline — a brief grounded in measurements is a plan; one grounded in vibes is a
mood board. **This is read-only — do not edit any file. The user picks the direction; you
propose, recommend, and stop.**

Scope: $ARGUMENTS

If no scope was given, brief the whole app's primary surface. If the conversation already holds
scan findings, build on them; otherwise run a quick pass first (screenshots at two widths, the
spacing/type/color censuses, the Life and Distinctiveness quick-estimates) so the brief answers
problems that actually exist.

## Phase 1 — Extract the project's intent

The anti-vibes rule that matters most here: **extract what this product is trying to be before
proposing what it should look like.** A brief that ignores the project's own identity produces
the AI-default look with extra steps.

1. Read the identity evidence: existing tokens and theme files, brand assets (logo, icons,
   illustrations), CLAUDE.md and README for how the team describes the product, marketing copy
   for how it talks.
2. Find **the best screen in the app** — the one that most looks like someone cared — and name
   what it does right. The brief should extend that screen's virtues, not overwrite them.
3. Characterize the product in the user's terms: who uses it, in what setting, and what feeling
   the interface should produce (calm authority, playful energy, dense efficiency). One
   sentence. Everything downstream justifies itself against this sentence.

## Phase 2 — Propose two directions

One **primary direction** and one **counter-proposal** — genuinely different answers to the same
sentence, not a strong option and a straw man. For each, specify concretely enough that
`/designmaxxing:redesign` could execute without a single taste decision left open:

- **Palette** — 4–6 named hexes with roles (ground, surface, ink, accent, semantic set).
  Neutrals hue-tinted toward the accent, never raw framework grays. State contrast pairs and
  confirm the core text/ground pairs pass WCAG 2.2 AA before proposing them.
- **Type pairing** — display, body, and data faces with full fallback stacks, weights, and the
  scale ratio. Say *why* each face fits the personality sentence — "it's clean" is not a why.
- **Space, radius, shadow language** — the base unit and scale, the 2–4 radii, the elevation
  story (or the flat one). These become the tokens.
- **Motion identity** — what animates (and what never does), signature duration and easing,
  the entrance story, the hover grammar. Motion is identity, not garnish — a brief without it
  produces a redesign that still feels dead.
- **Layout moves** — the 3–5 structural changes, named per screen ("collapse the double sidebar;
  promote the primary metric to a masthead; one CTA above the fold").
- **What to deliberately keep** — the things already working, listed so the redesign doesn't
  churn them. Respect for the existing design is what separates a redesign from a replacement.

Slop tells (stock framework palette, template gradients, emoji as icons, the default typeface
alone) are **forbidden as proposals** — unless the user explicitly asked for one, in which case
their word wins and you execute it well.

If reference points help, fetch them (WebSearch/WebFetch) — but cite references for a *quality*,
never "make it look like X".

## Phase 3 — Recommend and hand off

1. Recommend one direction, in two sentences, tied to the personality sentence and the measured
   findings it fixes.
2. End with the exact execution scope for `/designmaxxing:redesign`, phased the way the method
   skill demands: **tokens first** (the palette, type, space values as named tokens),
   **components second** (buttons, cards, nav onto the tokens), **motion last** (the identity
   applied to states and entrances). List the files each phase touches where you can.

Then stop. Do not begin implementing — the user chooses, then `/designmaxxing:redesign`
executes.

## Recommended next step

Close by printing one line — `→ Recommended next: …` — chosen by what you produced:

- Direction chosen → `/designmaxxing:redesign` with the phased scope from Phase 3.
- The findings underneath feel thin → `/designmaxxing:scan` first, so the brief answers
  measurements instead of impressions.
- Token drift is the real blocker → `/designmaxxing:retheme` before any visual direction.
- The user wants to feel the difference first → `/designmaxxing:roast` on the current state,
  for the before-picture in plain English.
