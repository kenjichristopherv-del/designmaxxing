---
description: Explain a design principle, finding, or concept against your actual code — how it works, why it matters here, and how to spot it next time.
argument-hint: "[the concept or finding, e.g. 'visual hierarchy', 'why skeletons beat spinners']"
allowed-tools: Read, Grep, Glob, Bash, WebFetch, WebSearch, TodoWrite
---

# Explain it against your code

Teach the principle where the reader lives: in their own files. Follow the
`design-review-method` skill's plain-English-first rule throughout — this command exists for
the person who was just handed a finding and wants to actually understand it. **This is
read-only — do not edit any file. Teach; do not fix.**

The concept: $ARGUMENTS

If no concept was given, ask what they'd like explained — or, if the conversation holds a recent
scan or roast, offer its top finding as the default. If the concept is broad ("typography"),
narrow it to the single facet their codebase most exhibits and say you're doing that.

## 1 — Find it in this codebase

Generic explanations are what search engines are for. Ground yours here:

- Grep for where the concept **applies** in this repo, and where it's **violated** — both, when
  both exist. Real `file:line` references, quoted snippets, actual computed or declared values.
- Find the best example in the repo too, if one exists: "your `Button` in
  `components/ui/button.tsx:12` already does this right — the fix is making `Card` match it."
  A local positive example teaches faster than any external one.
- If the concept genuinely doesn't appear in this codebase, say so and teach it against a
  minimal invented example clearly labeled as such — never present invented code as theirs.

## 2 — Explain it in layers

Lead plain, deepen honestly, translate jargon in the same breath it first appears:

1. **In plain English** — one or two sentences a non-designer could repeat at lunch. "Visual
   hierarchy means the page tells your eye where to go first. When everything is the same size
   and weight, nothing is first, so the reader does the sorting the design should have done."
2. **How it works** — the mechanism: what the eye or the browser actually does, and which CSS
   properties or markup patterns drive it.
3. **Why it matters *here*** — the effect on this product's real user, tied to the examples
   from step 1. Not "hierarchy is important" but "on your pricing page, the plan name, the
   price, and the fine print are all 16px/400 — a buyer can't scan the difference between the
   three plans without reading all of them."
4. **The fix pattern** — what the correct version looks like, as a pattern with a short code
   sketch. Show the shape of the fix; leave applying it to `/designmaxxing:redesign` or the
   user.
5. **How to spot it next time** — the one-line heuristic and, where one exists, the measurement:
   "squint at the screenshot; if nothing pops, hierarchy is flat" / "census the font sizes; more
   than ~8 means the scale has dissolved."

## 3 — Cite the canon, when there is one

When the concept has a canonical source, cite it and link it (WebFetch/WebSearch to verify):
Laws of UX for the perception laws (Fitts, Hick, proximity), WCAG 2.2 for the accessibility
bars and the exact ratios, MDN for the platform mechanics (stacking, `prefers-reduced-motion`,
View Transitions), the platform HIGs where convention is the point. One or two sources, the
authoritative ones — a bibliography is not an explanation. If sources disagree (contrast math is
the classic case: WCAG 2.2 vs APCA), say so plainly and say which one is the compliance bar.

Never invent a citation, a statistic, or a "studies show". A principle explained honestly from
mechanism beats one propped up by a fabricated number.

## Report

Deliver the explanation as the five layers above, with the `file:line` examples inline. Close
with:

- **In your codebase** — the two or three places to look at again with new eyes, as a checklist.
- **Still fuzzy?** — the one follow-up question you'd expect, pre-answered in a sentence.

## Recommended next step

Close by printing one line — `→ Recommended next: …` — chosen by what was explained:

- The violation is real and scoped → `/designmaxxing:redesign` scoped to it, or the audit
  command that owns the category (`/designmaxxing:type`, `:color`, `:layout`, `:states`).
- They want to see how widespread it is → the measuring command for that category, or
  `/designmaxxing:scan` for the whole picture.
- The concept was a term from a report → back to that report's next-step line; this was a
  detour, not a new thread.
