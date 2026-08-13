---
description: Distinctiveness audit — score how templated the UI looks by counting the known AI-default tells, matched on computed values, not vibes.
argument-hint: "[url or route to audit, e.g. 'http://localhost:3000']"
allowed-tools: Read, Grep, Glob, Bash, TodoWrite, mcp__playwright
---

# Slop audit

Measure how much this UI looks like every other AI-built UI. Follow the `design-review-method`
skill for the finding format; the Distinctiveness composite is owned here. The tells below are
weighted and specific precisely so the answer is a count, not an opinion — "looks generic" is a
mood; "six tells totaling −54, no positive signals" is a finding.

**This is read-only — do not edit any file.** Screenshots go to the scratch directory, named
`route_viewport_state.png`.

Scope: $ARGUMENTS

If no target was given, audit the landing/home route first — slop concentrates there — then one
inner screen. Prefer a running app with a browser tool (Playwright MCP or Chrome DevTools MCP)
so tells are matched on **computed values**; without one, run the static pass and say the
computed-value confirmation is **Not measured**.

## Phase 0 — Read the intent first

**A tell the user chose deliberately is not slop.** Before deducting anything, read CLAUDE.md,
brand docs, the token files, and any design notes in the repo. If the purple gradient is the
brand, it scores zero deductions — the user's stated direction always wins, and this audit
measures *defaults shipped unexamined*, not choices. Note which tells were excused and why.

## Phase 1 — The tell census (channels A + B)

Start at 100. Match each tell in source (channel A) and confirm on computed values in the
browser (channel B) — people copy the Tailwind palette into custom CSS, so class-name greps
alone under-count.

| Tell | Detection | Weight |
|---|---|---|
| Stock Tailwind palette shipped as-is | Computed hexes matching the defaults: slate `#f8fafc #f1f5f9 #e2e8f0 #64748b #1e293b #0f172a`, blue-500 `#3b82f6`, indigo/violet/purple `#6366f1 #8b5cf6 #a855f7`, pink-500 `#ec4899`; untouched `tailwind.config` with no `extend.colors` | −15 |
| Purple→blue or purple→pink hero gradient | Gradient stops in the 250–290° hue band paired with 220–250° or pink; grep `from-blue-`/`from-indigo-`/`via-purple-` | −15 |
| Inter, Poppins, Roboto, or Geist as the only typeface | Font census: one family, no display face, default weights | −10 |
| Emoji as icons | Emoji codepoints inside headings, feature cards, or buttons (🚀 ✨ 💡 in the DOM text) | −10 |
| The canned landing skeleton | Hero + exactly three feature cards + logo strip + pricing + FAQ, in that order — judge from the screenshot | −10 |
| Colored 3–4px left-border accent strips on cards | Computed `border-left-width: 3px|4px` + colored, on card-like elements | −8 |
| Gradient hero text | `background-clip: text` / `bg-clip-text text-transparent` on the h1 | −6 |
| Uniform card recipe everywhere | 100% of cards sharing white bg + gray ~1px border + `rounded-xl` + `shadow-sm` | −6 |
| Glassmorphism pill badge over the h1 | `backdrop-blur` pill with a ✨/"New" label above the hero heading | −5 |
| Sloganeering copy | Headings containing Supercharge, Seamless, Unlock, Effortless, 10x, "Built for the modern…" | −5 |

Every deduction gets evidence: the computed value or DOM excerpt, a screenshot crop, and the
`file:line` that produced it.

## Phase 2 — The positive signals (up to +15)

Distinctiveness isn't just the absence of tells. Add back for identity that's actually there:

- A real display/body type pairing, loaded and rendering (confirm in the font census — a
  declared font that never loads counts for nothing).
- Neutrals hue-tinted toward the brand color instead of stock gray ramps.
- Bespoke illustration, photography style, or 3D — any proprietary visual asset beyond a
  wordmark.
- A motion identity: a signature transition or entrance used consistently (cross-check
  `/designmaxxing:life` output if available).
- A distinctive radius/border/texture language — sharp corners on purpose, hairline rules,
  grain — applied consistently rather than once.

## Phase 3 — Score and report

Sum to the **Distinctiveness score (0–100)** and place it in its band: **85+** distinctive ·
**60–84** competent but derivative · **35–59** template-adjacent · **below 35** slop.

Report in the skill's finding format: the score and band up front, then each tell found (with
evidence and weight), the tells excused by stated intent, the positive signals found, and —
the part that makes this actionable — **what distinctive would look like for this product**:
two or three directions argued from its actual domain, audience, and existing assets, not from
trends. That paragraph is the seed for `/designmaxxing:brief`. Close with **Scores**,
**Checked and clean** (tells checked and absent — say so; a clean sheet is a result), and
**Not measured**.

Then stop. Do not begin fixing. Offer `/designmaxxing:brief` or `/designmaxxing:redesign`.

## Recommended next step

Close by printing one line — `→ Recommended next: …` — chosen by what you found:

- Score under 60 → `/designmaxxing:brief` to pick a real direction before any pixel moves —
  fixing tells one at a time just produces politer slop.
- Score under 60 and the user already knows the direction → `/designmaxxing:redesign` with
  that direction stated.
- Tells are mostly palette/typeface defaults → `/designmaxxing:retheme`; the fix is a token
  system, not a page-by-page edit.
- Score 60–84 → name the one or two moves (a display face, tinted neutrals, one signature
  motion) that would cross into distinctive.
- Score 85+ → say so plainly; distinctiveness is not the problem. If the UI still
  disappoints, `/designmaxxing:life` or `/designmaxxing:scan` will find where the problem
  actually lives.
