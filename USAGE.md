# Using designmaxxing

A practical guide. For what each command measures, see the [README](README.md).

---

## Start here

```
/designmaxxing:help http://localhost:3000
```

Point it at a running page. It renders it, measures it, decides what's wrong, fixes it, and
shows you the before and after. For most people that is the whole product — everything else in
this plugin is for when you want to go deeper on one thing.

Two rules make it work:

**Give it a URL, not a file path.** The premise of this plugin is that source shows intent and
the browser shows what shipped. A path makes it guess. A URL makes it look.

**Have the app running first.** If it isn't up, the command's opening move is to work out how to
start it — and it will do that worse than you would. Start the dev server, then hand over the URL.

---

## When to reach past `help`

Use a specialist when you already know what you're asking about.

| You want to… | Command |
|---|---|
| Hear it straight, change nothing | `:roast` |
| A full graded report card | `:scan` |
| Turn findings into a creative direction | `:brief` |
| Apply a direction across the app | `:redesign` |
| Review a branch or PR before merging | `:diff` |
| Fix one axis | `:type` `:color` `:layout` `:motion` `:responsive` |
| Find dead interactions | `:life` |
| Check the states users actually meet | `:states` |
| See how templated it looks | `:slop` |
| Rationalise a token system | `:tokens`, then `:retheme` |
| Understand why something is wrong | `:explain` |

`:diff` is the one worth building a habit around — run it before you merge, like tests.

---

## The two habits that matter more than the commands

### 1. Give it a reference

Keep a folder of screenshots of interfaces you admire and point the tool at it. Taste is an
input you supply, not something a model invents. Without reference material, every generated UI
converges on the same defaults — and that is a missing-input problem, not a model problem.

This single habit does more for output quality than any command in this plugin.

### 2. Write down what is deliberate

The most valuable thing this tool can do is **refuse to change something**. It can only do that
if the code says so.

A real example. A dashboard page measured at 19 distinct font sizes and 16 spacing values —
textbook drift, and a naive pass would have "fixed" it. Its stylesheet opened with:

> *A deliberate port of that screen's own design rather than a translation into ours: the point
> of this page is that an operator who knows the original recognises it instantly. It is
> therefore the ONE page in this app that does not use the app's design tokens.*

Those 19 sizes were the job. Deliberate choice and genuine neglect measure identically; only the
prose separates them. `help` reads headers, docblocks and `CLAUDE.md` before proposing anything,
so a comment recording intent is what stands between your deliberate choice and a helpful
rewrite of it.

What survives that filter is better material anyway: outright defects, states nobody designed,
and accessibility gaps — none of which are ever in tension with a deliberate look.

---

## What it will not do

- **Presentation code only.** Styles, tokens, markup structure, class lists, motion. Never
  application logic, data fetching, routing, or business rules.
- **It will not overrule you.** Ask for the loud gradient and the loud gradient is correct. Your
  direction is the spec, not a finding.
- **No new dependencies or styling systems** without asking first. Tailwind stays Tailwind.
- **It will not claim a win it cannot see.** If the after-screenshot looks worse, it says so.

---

## Reading the output

**Zero findings is a real result.** A page that comes back clean gets told so, with the numbers.
Do not read a short report as the tool having failed to try.

**Expect the verify pass to find new bugs.** Putting a component into a state nobody had looked
at is how you discover the state was never handled — a readout that never clears, a class that
never comes off, a placeholder that outlives its data. That is the step working, not a
regression. A verify pass that never finds anything usually never really looked.

**Every finding carries a measurement.** "This felt cluttered" is not a finding here. "The
spacing values in this view are 6, 8, 10, 12, 13, 16 and 18px, so nothing aligns to a rhythm"
is. If you get an adjective without a number, push back — the tool is not doing its job.

---

## A worked shape

What a real pass tends to look like, end to end:

1. **Look** — render the route at 360, 768, 1440, both themes. First impressions, labelled as such.
2. **Measure** — distinct font sizes, the spacing histogram, the colour census, hover and
   focus coverage, token adoption percentage.
3. **Read the intent** — headers, docblocks, `CLAUDE.md`. Decide which findings are mistakes and
   which are decisions you are not party to.
4. **Change** — tokens first, then migrate usages, then components, then motion.
5. **Prove** — same routes, same widths, re-measured. Numbers moving, screenshots side by side.

Steps 1 and 5 are where the quality actually comes from. Reference input at the front, visual
verification at the back. Neither is improved by swapping models.
