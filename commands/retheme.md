---
description: Rationalize the de-facto tokens into a clean system — 14 grays become 4 — and migrate every usage, verified by screenshot diff.
argument-hint: "[optional scope, e.g. 'colors only' or 'spacing and radii' — defaults to everything /designmaxxing:tokens found]"
allowed-tools: Read, Grep, Glob, Bash, Edit, Write, TodoWrite, mcp__playwright
---

# Retheme

Turn the accumulated token chaos into a deliberate system and move the codebase onto it. Follow
the `design-review-method` skill for the evidence bar and the redesign discipline. This is a
refactor with a visual invariant: **the app should look the same after, except where the
mapping table says otherwise.**

**This command writes code — token definitions and their usages only.** It edits theme files,
stylesheets, and class lists to swap literals for tokens. It does not restyle components, touch
application logic, or make aesthetic decisions beyond the approved mapping. This is the
highest-blast-radius command in the plugin; silent visual drift across a whole app is its
failure mode, which is why every phase below has a gate.

Scope: $ARGUMENTS

If no scope was given, retheme everything the census surfaces — colors, spacing, radii,
shadows, and type sizes. If the census is overwhelming, propose starting with colors alone and
let the user widen it.

## Phase 1 — Extract

**No census, no retheme.** Reuse the `/designmaxxing:tokens` output if it exists in the
conversation; otherwise run the census now:

- Every literal value in use per dimension — hex/oklch colors, px/rem spacing, radii, shadow
  strings, font sizes — with a usage count and the `file:line` sites for each.
- Which values already flow through tokens (`var(--*)`, theme classes, `theme()`) versus raw
  literals, and where the project keeps its tokens today (CSS custom properties,
  `tailwind.config`, a theme module).
- Near-duplicates called out explicitly: `#f4f4f5` and `#f5f5f5` both in use is the disease
  this command treats.

## Phase 2 — Rationalize and get approval

Design the target system on paper before touching a file:

1. Map **every** de-facto value to its nearest sanctioned token. Present the full mapping
   table: `old value → new token → visual delta`, with usage counts. Flag every mapping where
   the change is perceptible — ΔE > 2 for color, more than 2px for spacing — because those are
   design decisions, not cleanups.
2. Name tokens semantically — `--surface`, `--text-primary`, `--space-4` — never by appearance
   (`--gray-3` breaks the first time the gray changes hue).
3. Put the system where the project already keeps one. Do not introduce a new theming
   mechanism; extend the existing convention.
4. Anything that resists mapping — a hex that could go two ways, a hardcoded value that looks
   intentional (a brand color in a one-off illustration, a third-party widget override) — goes
   on an **asks list**, not into a guess.

**Show the mapping table and stop. Migrate nothing until the user approves it.** The table is
the contract for everything after; an unapproved perceptible delta discovered in Phase 5 is a
defect of this phase.

## Phase 3 — Baseline

Screenshot the affected routes at 360, 768, and 1440, in both themes if the app has them, into
the scratch directory. This is the "before" set the visual invariant is checked against. If no
browser tool is connected, say so and get explicit go-ahead to proceed on build-and-grep
verification alone — a weaker guarantee, named as such.

## Phase 4 — Migrate mechanically, in batches

The migration is mechanical by design — the decisions were all made in Phase 2.

1. **Token definitions first.** Land the new tokens alongside the old values so nothing breaks
   mid-migration.
2. **Usages batch-by-batch** — per directory or per component family, sized so a batch is
   reviewable and revertible on its own. Prefer exact-literal matches: grep the value, replace
   with the token, count the replacements against the census count and account for any gap.
3. Anything ambiguous found mid-flight joins the asks list; it does not get guessed.
4. Run build and typecheck after every batch. A batch that breaks the build gets fixed or
   reverted before the next one starts.
5. When all batches land, remove the old orphaned definitions — dead tokens left behind are the
   seed of the next drift.

## Phase 5 — Prove

1. Re-screenshot the baseline set and diff against Phase 3. The target is **near-zero visual
   change except the approved deltas** from the mapping table.
2. Report every drift the diff finds, honestly, with the screenshot pair — then fix or revert
   it. Unapproved drift is never shipped as a bonus.
3. Re-run the census and lead with the numbers: `14 grays → 4 · 27 paddings → 8 · 9 radii → 3`,
   plus the literal-vs-token ratio before and after.
4. List every file touched, the asks list still open, and anything deliberately left unmapped.

## Recommended next step

Close by printing one line — `→ Recommended next: …` — chosen by what happened:

- Retheme clean, diffs match the table → `/designmaxxing:redesign` — it now has stable ground
  to build on.
- Visual diffs came back unexpectedly large → revert the offending batch and re-map; do not
  push through.
- Dark mode still lives as per-element overrides → a follow-up retheme scoped to theme tokens
  (`--surface`/`--text-*` pairs per scheme).
- Asks list has real design questions on it → `/designmaxxing:brief` to settle them as one
  direction instead of one-off answers.
