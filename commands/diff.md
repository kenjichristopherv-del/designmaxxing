---
description: Design review of uncommitted changes, a branch, or a PR — catches design regressions at the moment they're introduced.
argument-hint: "[branch, PR number, or path — defaults to uncommitted changes]"
allowed-tools: Read, Grep, Glob, Bash, TodoWrite, mcp__playwright
---

# Design review of a diff

Review only what changed, before it ships. Follow the `design-review-method` skill for the
severity rubric, the finding format, and the anti-vibes rules — the one rule holds at diff scale
too: no critique without a measurement. **This is read-only — do not edit any file.**

Target: $ARGUMENTS

If no target was given, review the working tree: unstaged plus staged changes (`git diff` and
`git diff --cached`). A branch name means diff against the default branch
(`git diff main...<branch>`); a number means a PR (`gh pr diff <number>`). If the diff is empty,
say so and stop — do not widen the review to the whole repo uninvited.

## Phase 1 — Resolve the changed surface

1. List the changed files and keep only the ones that touch the UI: components, templates,
   stylesheets, Tailwind classes in markup, token/theme files, fonts, images. Say what you
   excluded ("migrations and API handlers — no design surface").
2. Rank by blast radius: a token or theme file change touches every screen; a component in
   `ui/` or `components/` touches many; a page-level file touches one route.
3. Read each changed hunk **with its surrounding file**, not in isolation — a hardcoded `13px`
   is only a finding if the file (or the repo) has a spacing scale it ignores. Grep for the
   project's tokens first so you know what "on-system" means here.

## Phase 2 — Review the changed surface only

Findings must be *introduced or worsened by this diff*. Pre-existing problems next to the change
get one line at the end, not findings — that's what `/designmaxxing:scan` is for.

- **Hardcoded values where tokens exist** — new raw hexes, px values, shadows, or radii in a
  codebase that has `var(--*)`, a theme file, or a Tailwind config for exactly that value.
  Quote the token it should have used.
- **New interactive elements shipped dead** — a new button, link, card, or input with no
  `:hover`, no `:focus-visible`, no transition, no disabled/loading treatment in the diff or in
  the classes it references. A component born without states stays dead for years.
- **Missing lifecycle states** — a new data-driven view with no loading, empty, or error branch;
  a new form with no designed validation state (browser-default bubbles count as none).
- **Off-scale values** — spacing or font sizes that don't fit the scale the repo already uses.
  Measure: list the repo's existing scale, then the new value that misses it.
- **New color pairs** — compute contrast for every new text/background combination (WCAG 2.2 AA:
  4.5:1 body, 3:1 large text and UI components). New pairs in only one theme are a finding too —
  check the dark-mode variant if the repo has one.
- **Slop tells introduced** — stock framework palette hexes, a template gradient, emoji as
  icons, `Inter` added as the only face. Flag against the repo's existing identity.
- **Accessibility of new markup** — click handlers on bare `div`s, images without alt, inputs
  without labels, `outline: none` without a replacement, removed focus styles, `tabindex` > 0.
- **Motion hygiene** — new animation without a `prefers-reduced-motion` path when the repo has
  (or should have) one; transitions on layout properties (`top`, `width`) instead of
  `transform`/`opacity`.

## Phase 3 — Render the affected routes, if the app runs

If a dev server is running (or starts cheaply) and a browser tool is connected, screenshot only
the routes the diff touches — current state, plus the pre-change state if a stash/checkout
round-trip is cheap. Diff the pair per viewport (mobile + desktop is enough at diff scale).
Check the new interactive elements with a real hover and a real Tab stop.

If not, run static-only and open the report by saying so: "no runtime pass — state coverage and
computed contrast are from source only."

## Phase 4 — Report

Use the finding format from `design-review-method`, most severe first, each finding anchored to
the diff (`file.tsx:41` in the new version). Then:

- **Checked and clean** — this section carries real weight in a diff review. A clean diff gets
  told so, specifically: "4 new components, all with hover/focus/disabled states on the shared
  Button; no new raw values; both new color pairs pass AA." Praise is evidence too.
- **Pre-existing, not this diff** — one line each, no severities, so nearby debt is visible
  without polluting the review.
- **Not measured** — routes not rendered, themes not checked.

## Recommended next step

Close by printing one line — `→ Recommended next: …` — chosen by what you found:

- Drift is systemic (the diff follows bad local convention) → `/designmaxxing:scan` to measure
  the whole surface, then `/designmaxxing:retheme` if tokens are the cause.
- New component shipped dead → `/designmaxxing:redesign` scoped to that component's states.
- One finding worth understanding → `/designmaxxing:explain <it>`.
- Clean diff → say so and stop. A merge with no homework is the good outcome.
