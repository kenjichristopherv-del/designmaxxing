---
description: Audit every state a real user meets — loading, empty, error, pending, focus, disabled, success — the screens that only exist when things are slow, missing, or wrong.
argument-hint: "[flow or scope, e.g. 'the checkout flow' or '/settings'] (optional)"
allowed-tools: Read, Grep, Glob, Bash, TodoWrite, mcp__playwright
---

# State design audit

The happy path is the least of an interface. Audit the states users actually meet — waiting,
nothing-here-yet, something-went-wrong, and just-clicked — and whether each was designed or
merely happens. Follow the `design-review-method` skill for the severity rubric, the finding
format, and the anti-vibes rules. **This is read-only — do not edit any file.** Reaching
error and empty states may require throttling or invalid input — do it against dev/staging
seeds, never by submitting real data.

Scope: $ARGUMENTS

If no scope was given, audit the primary data-loading screen, one form, and one list that can
be empty. If no browser tool is connected, run channel A only — grep for the states in source —
and say plainly that no state was actually rendered or screenshotted.

## Phase 1 — Inventory the states in source

Channel A, before touching the browser — what does the code even *have*?

- Loading: skeleton/shimmer components, `Suspense` fallbacks, `aria-busy`, `isLoading`
  branches.
- Empty: zero-length conditionals (`items.length === 0`) — do they render a designed component
  with an explanation and a next action, or a bare string ("No results")?
- Error: error boundaries, form validation branches, `aria-invalid` usage, toast/inline error
  components.
- Missing entirely: a fetch with no pending branch, a list with no empty branch, a mutation
  with no error branch. Absence in source is a finding before anything renders.

## Phase 2 — Render the loading states

Throttle the network via the browser tool, navigate, screenshot at 500ms and 1500ms:

- Blank white at 500ms = no loading design — HIGH.
- A generic centered spinner for a >1s load = MED; content-shaped skeletons pass.
- **Skeleton fidelity.** Diff the skeleton screenshot against the loaded one — a skeleton whose
  layout differs from the final content makes the page appear to load twice. Quote the
  mismatch.

## Phase 3 — Render the empty and error states

- Reach each empty state (seeded-empty data, or a filter that matches nothing). A designed
  empty state explains what belongs here and offers the action that fills it; bare text is MED.
- Submit a form with invalid input. Browser-default validation bubbles = undesigned — MED.
  Designed validation is `aria-invalid` plus an inline message next to the field, in the
  semantic error color, that says how to fix it. Screenshot both.
- Kill or intercept a request; confirm an error boundary or designed error surface catches it
  rather than a blank region or a raw stack trace (raw trace = HIGH).

## Phase 4 — Measure feedback timing

- **The Doherty threshold** (feedback within ~400ms keeps a user in flow; the visible
  acknowledgment should land within ~100ms): click each primary action and screenshot at
  +100ms. No visual change — no disable, no progress, no optimistic update — is a MED finding
  with the flow named. Double-submit exposure (button stays clickable during the request) is
  HIGH.
- **Focus visibility.** Tab through each screen, screenshotting every stop. No visible change =
  BLOCKER. Also grep for `outline: none`/`outline:none` without a replacement — each hit is an
  instant BLOCKER at its `file:line`.
- **Disabled affordances.** Disabled controls need to look disabled and carry
  `cursor: not-allowed`; a control that looks live but ignores clicks is a trust bug — MED.
- Hover/active presence gets a one-line summary here; the full coverage measurement belongs to
  `/designmaxxing:life` — route depth there, don't duplicate it.

## Phase 5 — Judge the endings

The Peak–End rule: users remember the last screen of a flow disproportionately. Walk each key
flow to its end — order placed, settings saved, message sent — and screenshot it. A designed
confirmation states what happened and what's next; a silent redirect or a bare toast on a
consequential flow is MED, labeled with the flow.

## Phase 6 — Report

Use the finding format from `design-review-method`, most severe first — each finding names the
flow, the state, the screenshot pair, and the source `file:line` of the missing or undesigned
branch. These findings feed the Life Score's loading/empty/error component (20 of 100) — say
what you'd score it.

Close with **Checked and clean** (states that exist and are designed) and **Not measured**
(states you could not reach and why).

Then stop. Do not begin fixing. Offer `/designmaxxing:redesign`.

## Recommended next step

Close by printing one line — `→ Recommended next: …` — chosen by what you found:
- Interaction feedback thin overall → `/designmaxxing:life` for the full coverage measurement.
- Missing states confirmed → `/designmaxxing:redesign` scoped to states — highest return per line of code.
- Focus BLOCKERs → `/designmaxxing:redesign` scoped to focus, before anything aesthetic.
- States exist but feel abrupt → `/designmaxxing:motion`.
