---
name: distinctive-ui
description: >
  Correct-by-default identity patterns that prevent the AI-default look from being written. Use
  this skill whenever building new UI or reworking existing UI: a landing page, marketing site,
  dashboard, app shell, component library, or any screen where you are choosing colors, fonts,
  spacing, layout, or a theme rather than following an established design system. Trigger on
  'landing page', 'design', 'style this', 'make it look', 'make it pretty', 'color scheme',
  'palette', 'font', 'typography', 'theme', 'dark mode', 'hero', 'tailwind', 'css', 'ui',
  'branding'. Supplies the slop tells as prohibitions with their substitutions, where identity
  actually comes from, and the color/type/shape/copy disciplines that make a design read as
  chosen rather than generated — so /designmaxxing:slop never has anything to find.
---

# Distinctive UI

Left to defaults, AI-generated interfaces converge on one look: Inter on white, a purple-to-blue
gradient hero, three feature cards with emoji icons, stock framework grays. None of it is ugly.
All of it is the *most common* output, and readers have learned to recognize it — a templated
look now reads as "nobody designed this," which undercuts the product no matter how good the
code is. The failure isn't taste; it's that no choice was made.

## The one rule

**Derive the design from the product's own world — never from the model's defaults. If the user
pinned a direction, their words win; if not, the default look is not a neutral choice, it's the
most common choice, and it reads as generated.**

Before styling anything, answer in one sentence: what is this product's world — its materials,
its instruments, its vocabulary — and what should someone *feel* in the first two seconds?
Every choice below derives from that answer. A marine telemetry dashboard, a bakery's ordering
page, and a legal research tool share zero correct palettes.

## The tells — never write these unasked

Each of these is fine when the user asks for it. Unasked, each is a subtraction from the
design's credibility, and each has a cheap substitution:

| Never by default | Write instead |
|---|---|
| Purple→blue (or purple→pink) gradient hero | One committed brand hue, used with conviction — a flat field, a duotone, or a gradient within a single hue family |
| Stock framework palette used verbatim (`#3b82f6`, `#6366f1`, `#8b5cf6`, slate/zinc grays) | A palette mixed for this product; if on Tailwind, define it in the config — an untouched config is the tell |
| Inter/Poppins/Roboto as the only typeface | A real pairing: characterful display + workhorse body (+ a data face if numbers matter) |
| Emoji as icons (🚀 ✨ 💡 in cards and buttons) | One icon set, one stroke weight, consistent sizing |
| Hero + exactly three feature cards + logo strip + FAQ | Layout derived from the content's actual structure — what does *this* product need to show, in what order? |
| Gradient text on the headline (`bg-clip-text`) | Weight, size, and color contrast do the emphasis |
| "✨ Now with AI" glass badge pill above the h1 | Say the actual capability in the headline |
| Colored 3–4px left-border strips on cards | Elevation, spacing, or a designed header row |
| Uniform white card + gray border + `rounded-xl` + `shadow-sm` on everything | An elevation language: not everything is a card, and cards that differ in role differ in treatment |

## Where identity comes from

- **The subject's own world.** Materials, instruments, and vernacular are the source of
  distinctive choices: navigation charts and signal greens for a vessel monitor, flour tones
  and hand-set type for a bakery, ledger rules and tabular figures for a finance tool. If a
  choice could sit on any product, it isn't a choice yet.
- **One deliberate aesthetic risk, everything around it quiet.** An oversized display face, an
  unexpected accent, a strong grid break — one. A design that takes five risks is noise; a
  design that takes none is a template. Spend the boldness in one place and let the rest of
  the page hold still.
- **Distinctive ≠ maximal.** A restrained design executed precisely — exact spacing, a strict
  scale, one accent — is distinctive. Decoration added to a default is not.

## Color discipline

- **Structure: one brand hue + one or two accents + a neutral ramp + a semantic set**
  (success/warning/danger/info). Six unrelated hues is not a palette, it's an accident.
- **Tint the neutrals toward the brand hue.** Pure gray reads as unconsidered; a gray mixed
  2–5% toward the accent reads as designed. `color-mix(in oklab, var(--brand) 4%, #f5f5f5)`
  does it in one line.
- **Near-black over `#000`, near-white over stark white** for large surfaces: `#111`–`#1a1a1a`
  text on `#fafafa`-family grounds. Pure black on pure white glares; in dark mode, pure `#000`
  backgrounds kill elevation.
- **Commit semantic tokens on day one:**

```css
:root {
  --brand: oklch(0.62 0.15 200);
  --surface: …; --surface-raised: …;
  --text-primary: …; --text-secondary: …;
  --danger: …; --success: …;
}
```

  Components consume tokens, never raw values. Dark mode is a token redefinition in one
  block — never a scatter of per-element `dark:` overrides, which drift immediately.
- **Contrast is a floor, not a vibe:** WCAG 2.2 AA (4.5:1 body text, 3:1 large text and UI
  components) from the first draft.

## Type discipline

- **Pick the pairing before the first component exists.** Retrofitting a typeface re-breaks
  every measurement. If loading webfonts isn't worth it, deliberate system stacks cost
  nothing: `Charter, Georgia, serif` body under an `Avenir Next, Segoe UI, sans-serif`
  display; `ui-monospace` for data. Chosen system fonts beat a defaulted webfont.
- **One modular ratio, 5–8 sizes, stay on it.** Set the scale (1.2, 1.25, or 1.333), token
  the steps, and never type an ad-hoc `font-size` again. Twelve arbitrary sizes is the
  no-system tell.
- **Tracking at the extremes:** display text above ~40px gets `-0.01em` to `-0.03em`;
  uppercase labels get `+0.05em` to `+0.14em` and a smaller size. Defaults at both extremes
  read as unset.
- **Line length 45–90ch** for running text (`max-width: 65ch` is a fine default), line-height
  ~1.5–1.65 for body and ~1.1–1.25 for headings.
- **`text-wrap: balance` on headings, `text-wrap: pretty` on prose** — two lines of CSS that
  remove widows forever. Write them in the base styles on day one.

## Spacing and shape language

Decide once, tokenize, reuse — these three censuses are where drift shows first:

- **One base unit** (4px or 8px); every margin, padding, and gap is a multiple. A spacing
  scale of 6–12 values covers an entire product.
- **One radius family, 2–4 values** (e.g. 4/8/12/full), assigned by role — inputs and
  buttons share one, cards another. Eight arbitrary radii is drift; mixed sharp/pill/rounded
  siblings is worse.
- **One shadow language with one light source.** Two or three elevation steps, all offset the
  same direction, ideally tinted with the surface hue rather than pure black alpha.

## Copy is design material

- Name things by what users recognize, not how the system is built — "Notifications," not
  "Webhook config."
- **No sloganeering:** "Supercharge," "Seamless," "Effortless," "Unlock," "10x," "Built for
  the modern team" are the verbal equivalent of the purple gradient. Say the specific thing
  the product does for the specific user.
- Buttons say what happens: "Publish," "Save changes," "Delete 3 items" — never "Submit,"
  never "Click here."
- Microcopy carries the personality more than the hero does: empty states, confirmations,
  and errors are where a product sounds like itself.

## Before you ship the first screen

1. The one-sentence world/feeling answer exists, and the palette, pairing, and layout each
   trace back to it.
2. Zero tells from the table above are present unasked.
3. Tokens exist for color, spacing, radius, shadow, and type scale — and components consume
   only tokens.
4. The neutrals are tinted, the blacks and whites are near, contrast passes AA.
5. The type scale has ≤8 sizes on one ratio; headings balance; prose has a measure.
6. One deliberate risk is present, and it's the only loud thing on the page.
7. The copy names real things and promises nothing "seamless."
