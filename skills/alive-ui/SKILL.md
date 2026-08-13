---
name: alive-ui
description: >
  Correct-by-default interaction patterns that prevent dead UIs from being written. Use this
  skill whenever building or editing any interactive surface: a component, button, link, card,
  form, input, modal, dropdown, nav, tab, toggle, list, table row, or any page with an async
  boundary — data fetching, submission, routing, uploads. Also use when styling interactive
  elements in CSS or Tailwind, wiring click/submit handlers, or adding loading flows. Trigger on
  'component', 'button', 'form', 'modal', 'page', 'hover', 'focus', 'loading', 'skeleton',
  'spinner', 'transition', 'animation', 'interactive', 'click', 'submit', and any JSX, template,
  or CSS work on elements a user operates. Supplies the five-state element pattern, the 100ms
  feedback rule, designed empty/error/success states, the motion governor, and keyboard
  behavior — written the first time, so /designmaxxing:life never has anything to find.
---

# Alive UI

The default output of AI codegen is an interface that is technically interactive and visually
dead: buttons with no hover state, state changes that jump instantly, a blank region while data
loads, "No items" as an empty state, focus handled by whatever the browser felt like. Each
omission is invisible in the diff — nobody writes `hover: none` — which is why dead UIs are the
norm. The fix costs almost nothing at creation time and a full audit-and-retrofit later. This
skill is the creation-time version.

## The one habit

**Every interactive element ships with all five states — rest, hover, focus, active, disabled —
and every async boundary ships with loading, empty, and error designed. No element ships dead.**

An element missing a state isn't minimal, it's unfinished. When you create a button, the five
states are part of what "a button" means — the same way a function's error path is part of the
function.

## States by default

Write the five states in the same edit that creates the element, not as a later pass:

```css
.button {
  cursor: pointer;
  transition: background-color 160ms ease-out, transform 160ms ease-out,
              box-shadow 160ms ease-out;
}
.button:hover        { background-color: var(--accent-hover); }
.button:focus-visible { outline: 2px solid var(--accent); outline-offset: 2px; }
.button:active       { transform: scale(0.98); }
.button:disabled     { opacity: 0.55; cursor: not-allowed; }
```

The specifics that matter:

- **Every `:hover` is paired with a `transition`.** 120–300ms, `ease-out` for state changes.
  An instant jump between states is the single most reliable "feels dead" signal. Name the
  properties — never `transition: all`, which animates layout you didn't intend and costs
  performance you didn't budget.
- **`:focus-visible`, never bare `:focus`, and never `outline: none` without a replacement.**
  A branded outline — `2px solid` in the accent color with `outline-offset: 2px` — is two
  lines and turns a compliance chore into a design element. Removing the outline with nothing
  in its place is a BLOCKER in any review, including this plugin's.
- **`:active` gives pressed feedback.** A subtle `scale(0.98)` or a one-step darker fill. It
  reads as physical; its absence reads as a dead click.
- **Cursor tells the truth.** `pointer` on everything clickable — including JS-clickable divs,
  which don't get it for free — and `not-allowed` on disabled controls. Never `pointer` on
  things that do nothing.

In Tailwind, the same habit: `hover:` `focus-visible:` `active:` `disabled:` variants plus a
`transition-colors duration-150` land in the first class list, not a follow-up commit.

## Feedback within 100ms

When the user acts, something visible happens *now* — the Doherty threshold: response inside
~100ms feels instant, past ~400ms feels like the app hesitated. The await is not the feedback.

```jsx
<button disabled={saving} onClick={save}>
  {saving ? <Spinner size={14} aria-hidden /> : null}
  {saving ? 'Saving…' : 'Save changes'}
</button>
```

- **A submit disables its button and shows progress in the same frame the click lands.** Never
  a handler that awaits silently and lets the user click twice.
- **Prefer optimistic updates** where the mutation is safe to assume: update the UI, reconcile
  on response, roll back with an error message if it fails.
- **Skeletons shaped like the content for loads over ~1s** — a card-shaped shimmer where the
  card will be. A centered spinner tells the user "wait"; a skeleton tells them "here's what's
  coming" and prevents the layout jump when it arrives. Match the skeleton's dimensions to the
  real content or you ship two layout shifts instead of none.
- **Mark the region `aria-busy="true"`** while it loads, and never leave the boundary blank —
  a blank white region is the no-design state.

## Empty, error, and success are designed states, not fallthroughs

Every list you write will someday render with zero items, every fetch will someday fail, and
every flow ends somewhere. Write those branches with the same care as the happy path:

- **Empty = what this is + what to do next.** Never bare "No items" or, worse, nothing:

```jsx
{orders.length === 0 ? (
  <EmptyState
    icon={<InboxIcon />}
    title="No orders yet"
    action={<Button href="/products">Browse products</Button>}
  />
) : (…)}
```

- **Form errors are inline, specific, and marked up** — `aria-invalid` on the field, the
  message next to it naming what's wrong and how to fix it. Browser-default bubbles alone are
  the unstyled state; a toast that names no field is a scavenger hunt.
- **The success screen deserves design.** Peak–End: the confirmation is the moment the user
  remembers, and it's usually the barest page in the app. A designed confirmation — what
  happened, what happens next, one clear action — costs one component.

## Motion with a governor

Motion is seasoning: state changes, entrances, and attention get it; everything else stays
still. The governor, written from the start:

- **Animate `transform` and `opacity` only.** Animating `width`, `height`, `top`, `left`, or
  `margin` triggers layout every frame and janks on mid-range hardware. An accordion animates
  `grid-template-rows: 0fr → 1fr` or a measured `transform`, not `height` from `auto`.
- **Every decorative animation respects reduced motion from day one:**

```css
@media (prefers-reduced-motion: no-preference) {
  .card { animation: rise-in 400ms ease-out both; }
}
```

  Feedback transitions (hover, focus) may stay; decorative movement (entrances, parallax,
  loops) must be inside the gate. Retrofitting this later means auditing every animation
  you ever wrote.
- **Durations scale with distance and size.** Hover feedback 120–200ms; small element
  entrances 200–300ms; full-panel or page transitions 300–500ms. Uniform 300ms everywhere
  reads as robotic; over ~500ms on hover reads as sluggish.
- **One signature easing per project.** Pick it once — e.g. `cubic-bezier(0.2, 0, 0, 1)` —
  token it as `--ease-out-brand`, use it everywhere. `linear` is for spinners and marquees,
  nothing else.

## Keyboard is a first-class input

- Everything clickable is reachable by Tab and operable by Enter/Space. If you wrote
  `onClick` on a `div`, you owe it `role="button"`, `tabIndex={0}`, and a keydown handler —
  or, nearly always better, it should have been a `<button>`.
- **Esc closes what opened** — modal, dropdown, drawer — and clicking the backdrop does too.
- **Focus goes somewhere deliberate.** Opening a modal moves focus into it and traps it;
  closing returns focus to the trigger. A modal that strands focus behind the overlay is
  broken for every keyboard user, invisibly.
- Tab order follows visual order; if you reordered elements with CSS, check the sequence
  still reads top-to-bottom, left-to-right.

## Component definition of done

Before calling any interactive component finished:

1. Hover state exists, with a named-property transition of 120–300ms.
2. `:focus-visible` styled, branded, never removed without replacement.
3. `:active` gives pressed feedback.
4. Disabled state is visually muted with `cursor: not-allowed`.
5. Cursor is honest on every element.
6. Any action shows visible feedback within 100ms — disable + progress, or optimistic.
7. Loads over ~1s show a content-shaped skeleton, region marked `aria-busy`.
8. Empty and error branches render designed states with a next action.
9. Decorative motion is gated behind `prefers-reduced-motion` and animates transform/opacity.
10. Tab reaches it, Enter/Space operates it, Esc closes it, focus returns home after.
