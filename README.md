# designmaxxing

**Frontend design review for Claude Code.** Commands that scan the real rendered UI, measure
what's wrong — dead interactions, template-default looks, drifting tokens — and redesign it in
your codebase with before/after proof, plus skills that make Claude build alive, distinctive
interfaces by default.

Built for software engineers and vibe coders alike: every finding leads with a plain-English
sentence, and every number comes with the screenshot that proves it.

---

## Why this exists

Ask any AI to "make this look better" and you get a mood board in prose. "The spacing feels
inconsistent." "Consider a more modern palette." Sometimes it edits, and your app comes back
wearing the same purple-gradient, Inter-only, three-feature-card look as everyone else's —
because without evidence, a redesign is just the model's taste, and the model's taste is the
most common taste in the world.

The problem isn't the model. It's the prompt.

designmaxxing encodes the discipline a real design engineer applies:

- **No critique without a measurement.** Every finding names a selector, a `file:line`, a
  measured value, and what it should be — with a screenshot. "27 distinct padding values, 41%
  off the 4px grid" is a finding; "feels cluttered" is a mood.
- **No redesign without a before/after.** Every applied change re-renders the app and proves
  itself with a screenshot contact sheet and score deltas, per breakpoint. "Done!" is not
  evidence.
- **Render before you judge.** Source shows intent; the browser shows what users get. The two
  diverge constantly — the font that never loaded, the hover rule the cascade ate.
- **Judgment gets labeled.** Hierarchy and personality are real and partly unmeasurable, so
  taste is reported as **Taste** — separate from the measured findings, never dressed up as one.
- **The user's direction always wins.** If you asked for the purple gradient, the purple
  gradient is correct. designmaxxing redesigns toward *your* product's identity, not the
  model's defaults.
- **Zero findings is a valid result.** A well-designed screen gets told so, with the numbers
  that prove it was actually checked.

## Install

```
/plugin marketplace add kenjichristopherv-del/designmaxxing
/plugin install designmaxxing
```

Or, for local development:

```
/plugin marketplace add ~/Developer/designmaxxing
/plugin install designmaxxing
```

For the full experience, connect a browser tool — [Playwright
MCP](https://github.com/microsoft/playwright-mcp) or [Chrome DevTools
MCP](https://github.com/ChromeDevTools/chrome-devtools-mcp). Without one, every command still
runs its static pass and says honestly what it couldn't measure.

## New here? Type one thing

```
/designmaxxing:help http://localhost:3000
```

That is the whole product for most people. It looks at your app, measures what's actually
wrong, fixes it, and shows you the before and after. It never hands you a list of other
commands to go and run — everything below is for when you want to go deeper on one thing.

Want the honest version first, without anything being changed?

```
/designmaxxing:roast http://localhost:3000
```

A candid, funny, evidence-backed teardown of your UI — every burn footnoted with the number
that earned it ("your buttons have never once reacted to a mouse — hover coverage: 0 of 41"),
ending with the three changes that would matter most. If the design is actually good, the roast
turns into a toast and says so with the same numbers.

## The two scores

designmaxxing computes two numbers nothing else on the market measures:

- **Life Score (0–100)** — how alive the UI feels: hover coverage, transition presence,
  loading/empty/error design, focus states, animation census. A page where nothing reacts,
  nothing eases, and loading is a blank white screen scores like it feels: dead.
- **Distinctiveness (0–100)** — how templated it looks: stock framework palettes matched on
  computed hex values, template gradients, emoji-as-icons, the default typeface alone. Below
  35 is slop; above 85, the design could only be yours.

## The commands

Type `/design` in Claude Code and they'll all filter into view.

**The loop**

| Command | What it does |
|---|---|
| `/designmaxxing:help` | **The one that finishes.** Measures, decides, applies and proves it — the whole loop in one command, in plain English |
| `/designmaxxing:roast` | The teardown, read-only — funny on the surface, measured underneath |
| `/designmaxxing:scan` | The full 4-channel audit: graded report card across nine categories, findings ledger, prioritized redesign brief |
| `/designmaxxing:brief` | Turns findings into a creative direction — palette, type pairing, motion identity — and stops for your pick |
| `/designmaxxing:redesign` | The closer, and a writing command: applies the direction in-repo, re-renders, and proves it with a before/after contact sheet |
| `/designmaxxing:retheme` | The other writing command: rationalizes your de-facto tokens (14 grays → 4) and migrates every usage, verified by screenshot diff |
| `/designmaxxing:diff` | Design review of uncommitted changes, a branch, or a PR — catches new components shipped dead, at the moment it happens |
| `/designmaxxing:explain` | Explains a design principle or finding against *your* code, so you catch it yourself next time |

**By dimension**

| Command | What it measures |
|---|---|
| `/designmaxxing:life` | The dead-UI audit → the Life Score, with per-element evidence of missing hover, focus, and feedback states |
| `/designmaxxing:motion` | Animation quality — easing, durations, choreography, jank sources, reduced-motion compliance |
| `/designmaxxing:slop` | Distinctiveness — the template tells found, weighted and scored, and what a distinctive direction would look like for *this* product |
| `/designmaxxing:layout` | Hierarchy and spacing — the spacing histogram, alignment clustering, density, above-the-fold clarity |
| `/designmaxxing:type` | Typography — scale fit, line length and height, weight usage, font loading, generic-stack detection |
| `/designmaxxing:color` | Palette census in OKLCH, WCAG 2.2 AA contrast (APCA advisory), semantic discipline, dark-mode quality |
| `/designmaxxing:states` | Loading, empty, error, focus, disabled — every state a real user will meet, screenshot-verified |
| `/designmaxxing:responsive` | The breakpoint sweep 320→1920 — overflow bugs, mobile nav quality, fluid type, touch targets |
| `/designmaxxing:tokens` | Design-system health — de-facto tokens vs declared ones, drift censuses, and the rationalization proposal `retheme` executes |

Every command is read-only except `redesign` (which applies a direction and proves it) and
`retheme` (which shows you the token mapping table and asks before rewriting anything).

## The skills

Skills load automatically when Claude detects the relevant work — no command needed.

| Skill | Triggers on |
|---|---|
| **alive-ui** | Building or editing any interactive component. Ships every element with all five states — rest, hover, focus, active, disabled — and every async boundary with loading, empty, and error designed |
| **distinctive-ui** | Styling new UI. Prohibits the template tells and derives the design from your product's world instead of the model's defaults |
| **design-review-method** | Any design review — the evidence discipline, the severity rubric, the scoring model, and the finding format the commands share |

`alive-ui` is the quiet one that matters most. It doesn't wait for an audit; it makes the
hover state, the focus ring, and the loading skeleton exist the first time the component is
written — which is the difference between a Life Score you fix and one you never had to.

## Try it

```
/designmaxxing:help <url>        # start here — it fixes it and shows you
/designmaxxing:roast <url>       # if you want the honest version, read-only
/designmaxxing:scan <url>        # if you want the full report card
/designmaxxing:life <url>        # start here if your app feels dead and you want to know why
/designmaxxing:diff              # start here before you commit UI changes
```

A good workflow: `scan` for the report card, `brief` for a direction you actually like,
`redesign` to apply it with proof, and `diff` on every UI change after that so it never
regresses.

**See → judge → change → prove.** The commands come as a loop. A measurement is what makes a
critique actionable; a baseline is what makes a redesign checkable; the before/after contact
sheet is what makes it real. A redesign you've watched improve the numbers is a redesign you
can actually ship.

## Prompt catalog

**[PROMPTS.md](PROMPTS.md)** — the design-review prompts as raw, copy-pasteable text. Use them
in Cursor, Copilot, ChatGPT, or anywhere else; edit them for your stack; or read them to see
exactly what the commands are asking for.

Start with [Section 0, the preamble](PROMPTS.md#0-the-preamble). It's the highest-leverage
paragraph in the repo — paste it before any design prompt in any tool and the output stops
being a mood board.

## What this is not

This is a rigorous reviewer and a careful redesigner. It is **not** a designer with your
users' eyes, and it is not user research — it measures craft, not product-market fit.

Aesthetic judgment from a model is a taste, and it's labeled as one: **Taste** findings are
opinions to weigh, measured findings are facts to check. It also can't feel a janky scroll on
a three-year-old phone or see your brand the way your customers do. Check the label before you
act, and keep a human eye on the after screenshots — that's why they're the deliverable.

## Contributing

New checks, better thresholds, and corrections are all welcome — especially reviews where the
judgment went confidently wrong, since those become anti-vibes rules in `design-review-method`.
Open an issue with the screenshot, what was reported, and what a designer actually said.

## License

Apache 2.0 — see [LICENSE](LICENSE).
