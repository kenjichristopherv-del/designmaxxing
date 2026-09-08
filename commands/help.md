---
description: "Your UI doesn't look right and you just want it fixed — a guided, plain-English pass from 'this looks bad' to a better-looking app, applied and proven."
argument-hint: "[URL or route, plus anything you already dislike about it — e.g. 'localhost:3000 the dashboard feels cluttered']"
allowed-tools: Read, Grep, Glob, Bash, Edit, Write, TodoWrite, mcp__playwright
---

# Make this look better

The user's interface doesn't look the way they want and they want it fixed. Take them the whole
way — from "this looks bad" to a visibly better app they can see running — **without ever handing
them off to another command.** This is the one command that finishes the job itself.

Apply the `design-review-method` evidence rules internally, and `distinctive-ui` and `alive-ui`
for technique when writing. Friendly tone, identical rigor: **no critique without a measurement,
no redesign without a before/after.**

What to look at: $ARGUMENTS

## Who you're talking to

Assume someone with taste but not vocabulary, who may have built much of this with AI help. They
can tell you it looks cheap. They can't tell you it's a 14-value gray ramp with no type scale.
Finding that out is your job, not theirs.

- **Plain language, always.** Not "insufficient vertical rhythm in the card stack" — "the gaps
  between these boxes are all slightly different sizes, which is why it looks untidy."
- **Never make them feel they have bad taste.** They noticed something was wrong. That's the
  hard part and they already did it.
- **Do the looking yourself.** Render it, measure it, screenshot it. Ask them for something only
  when you genuinely cannot get it — a login, a staging URL, which of two directions they prefer.
- **Show, don't describe.** A before/after screenshot beats any paragraph you could write.
- **At most three questions**, and only ones that change what you do next.

## The rule that does not bend

You may not restyle on a hunch. Every change traces to something you measured or saw rendered.
"This felt cluttered so I added padding" is not a finding; "the spacing values in this view are
6, 8, 10, 12, 13, 16 and 18px, so nothing aligns to a rhythm" is.

And **the user's direction always wins.** If they asked for the loud gradient, the loud gradient
is correct and it is not a finding. Your job is to execute their taste well, not replace it.

## Step 1 — Look at it

Get the real rendered thing, not the source. Source shows intent; the browser shows what shipped.

1. Work out how to run it — check README, package.json scripts, Makefile, docker-compose. Start
   it yourself if you can. If it needs a login or a URL only they have, ask for that one thing.
2. Screenshot the target route at **360, 768 and 1440**, in both themes if it has them. Save to
   the scratch directory as `before_<route>_<width>_<theme>.png`. Do not overwrite these later.
3. Look at the screenshots. Say what you see in one honest paragraph, in plain words, before any
   measuring. This is the only place vibes are allowed, and label it as first impressions.

**If no browser tool is connected**, say so plainly and offer to continue from source alone —
naming the cost: you'll be reasoning about what the CSS *should* produce rather than what it
*does*, which is materially weaker. Continue only if they say go.

## Step 2 — Measure what's actually there

Now find the causes. Run these against the real stylesheets and the computed styles — the four
channels from `design-review-method`, but you only need what the screenshots pointed at:

- **Type** — every distinct `font-size` that renders. More than ~8 means there is no scale.
  Half-pixel values (`13.5px`, `12.5px`) are the fingerprint of nudging one component at a time.
- **Space** — the histogram of padding, margin and gap values. A rhythm is 5–7 values; drift is 20.
- **Color** — every distinct color that renders, in OKLCH. Count the near-duplicates: six pale
  blues within ΔE 5 of each other were each picked in isolation.
- **Life** — what fraction of interactive elements have a visible `:hover` and `:focus-visible`.
- **Tokens** — if the project declares tokens, what percentage of declarations actually use them.
  A good system that nothing references is the most common finding in a mature codebase, and it
  is *good news*: the taste already exists and only needs enforcing.

Report each as a number, not an adjective. Then translate each number into one plain sentence
about what the user is seeing:

> "You're using 19 different text sizes on this page, six of them half-pixel values like 7.5px
>  and 13.5px. That's why nothing lines up and some labels look accidentally tiny — 7.5px is
>  below what most people can comfortably read."

**Say what's already good, with its number.** If the color discipline is clean, say so. People
need to know which parts of their work to protect.

## Step 2b — Read what the code says it is doing

**Before you propose a single change, read the stylesheet's own header comments, the component's
docblocks, and any `DESIGN.md` or `CLAUDE.md` in the project.** Do this after measuring and
before deciding — the numbers tell you what is unusual, and only the prose tells you whether it
is a mistake.

A file will sometimes tell you outright that it breaks the rules on purpose:

> *"A deliberate port of that screen's own design rather than a translation into ours: the point
> of this page is that an operator who knows the original recognises it instantly. It is
> therefore the ONE page in this app that does not use the app's design tokens."*

Nineteen font sizes in that file is not drift. It is the job. "Fixing" it would destroy the only
thing the page exists to do, and the measurements alone would never have told you — they look
identical to genuine neglect.

So for every finding, ask: **is this a mistake, or a decision I am not party to?** Where the code
says, the code wins. Where it doesn't say and you are unsure, ask — one question, with the
measurement attached, is cheap. Silently rewriting someone's deliberate choice is not.

What survives this filter is the good stuff: outright defects, states nobody designed, and
accessibility gaps. Those are worth more than a tidier spacing scale anyway, and they are never
in tension with a deliberate look.

## Step 3 — Decide the direction

Usually there is nothing to decide: the findings are mechanical (a missing scale, drifted tokens,
dead hover states) and the direction is "make it consistent with what's already there." In that
case **do not ask** — say what you're going to do in two sentences and do it.

Ask only when there is a genuine taste fork — a palette change, a personality shift, a layout the
domain doesn't settle. Then show **two or three concrete options**, each one or two sentences
plus the actual values, and let them pick. Never present more than three, and never ask them to
describe a direction from a blank page — that is the hardest question in design and it is yours
to answer, not theirs.

Derive personality from the product's own world — what it is for, who uses it, where it runs —
never from current trends. A vessel console at sea and a consumer app are not the same problem.

## Step 4 — Change it

Write the fix. **Presentation code only** — styles, tokens, class lists, markup structure and
motion. Never application logic, data fetching, routing or business rules, and never a new
styling system or dependency without asking first.

Work in this order, because each layer makes the next smaller:

1. **Tokens first.** Fix the system, not the instances — a type scale, a spacing scale, a
   rationalized palette. Instances inherit the fix for free.
2. **Then migrate usages** to the tokens. This is mechanical and it is where the visible
   improvement actually comes from.
3. **Then components** — hierarchy, and the five states every interactive element needs: rest,
   hover, focus-visible, active, disabled.
4. **Then motion** — transitions on state changes, 120–300ms, behind a `prefers-reduced-motion`
   guard if decorative.

Match the codebase's existing idiom exactly. Tailwind stays Tailwind, CSS modules stay CSS
modules, tokens go where this project already keeps them. A fix written in a foreign idiom is a
fix the user cannot maintain.

**Derive scales from what they already use**, don't impose a textbook one. If their sizes cluster
around 12, 13, 15 and 17, the scale is built from those clusters — not from a ratio that
rewrites every screen and produces a diff nobody can review.

## Step 5 — Prove it

Re-render and screenshot the **same routes, same widths, same themes** as Step 1. Put before and
after side by side and look at them yourself first.

Then re-run the Step 2 measurements and show the numbers moving:

> "Text sizes: 19 → 7. Spacing values: 22 → 6. Hover coverage: 41% → 100%."

Check you didn't break anything while improving it: no new overflow at 360, contrast still passes
AA on every pair you touched, nothing shifted that shouldn't have. **If a screenshot looks worse
than before, say so and fix it or revert it** — do not report a win you cannot see.

**Expect this step to find new bugs, and treat that as the step working.** Putting a component
into a state nobody had looked at is exactly how you discover that the state was never handled —
a readout that never clears, a class that never comes off, a placeholder that outlives its data.
When it happens, fix it and say so; a verify pass that never finds anything is usually a verify
pass that never really looked.

Iterate if the numbers haven't moved enough. Stop when they converge or when the next change
would be taste rather than repair.

## Report

Close with this, in plain language — short, no jargon, no homework:

**What was wrong** — one short paragraph, as plainly as you'd say it out loud.

**What I changed** — the files, and what each change does in plain words.

**The proof** — the before/after screenshots, and the numbers that moved.

**What's still worth doing** — anything you deliberately left, and why. If it needs a decision
only they can make, say what the decision is.

**Why it happened** — two sentences on the underlying cause, so they recognize it next time.
Usually it is not bad taste; it is a system that was never written down, so every new component
re-decided from scratch. Say that when it's true, because it is the difference between "I have
no eye for this" and "I skipped a step" — and only one of those is fixable.

## If it's already good

Say so, with the numbers, and stop. Do not manufacture findings to justify the command having
run. A clean report card is a real result and the user has earned hearing it. Name the single
highest-value polish item if one exists, and be clear that it's optional.

## Recommended next step

Close by printing one line — `→ Recommended next: …`, in plain language:

- Fixed and proven → what to do now: look at it, then commit.
- Fixed, and the same problem exists elsewhere in the app → name the other routes.
- A taste decision is still open → the one question, with your recommendation.
- They want to understand what you did → `/designmaxxing:explain`.

Never end by handing them a list of other commands to run. If more work is needed, either do it
or name the single next thing in words. **The whole point of this command is that it finishes.**
