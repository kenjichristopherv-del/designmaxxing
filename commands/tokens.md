---
description: Audit design-system health — extract the tokens the app actually uses, diff them against the ones it declares, and produce the rationalized system retheme can execute.
argument-hint: "[scope, e.g. 'colors only' or 'the component library'] (optional)"
allowed-tools: Read, Grep, Glob, Bash, TodoWrite, mcp__playwright
---

# Design token audit

Every app has a design system — the question is whether it's the one in the config or the one
that grew in the dark. Extract the de-facto system from what renders, diff it against the
declared one, and measure the drift. Follow the `design-review-method` skill for the severity
rubric, the finding format, and the anti-vibes rules. **This is read-only — do not edit any
file.** This command is the natural feeder for `/designmaxxing:retheme` — its final section is
written to be executed.

Scope: $ARGUMENTS

If no scope was given, audit the full system: color, spacing, radii, shadows, type. Without a
browser tool this command still works well — channel A covers declared tokens and hardcoded
values — but the de-facto census (what actually renders after the cascade) needs channel B;
say which half ran.

## Phase 1 — Read the declared system

Channel A: collect what the codebase *claims* its system is — CSS custom properties, the
Tailwind config's `extend` block, theme files, styled-system objects, vendored UI-kit tokens.
Note where tokens are defined and whether semantic names exist (`--surface`, `--text-primary`)
or only raw scales (`--gray-100`…). An untouched framework config (no `extend.colors`, no
`extend.fontFamily`) is itself a finding — the system was never made theirs — and evidence for
`/designmaxxing:slop`.

## Phase 2 — Extract the de-facto system

Channel B: census the computed values across in-scope routes — every color, every
margin/padding/gap, every `border-radius`, every `box-shadow`, every (font-size, weight,
line-height) tuple. This is the system users actually experience. Cluster each census into the
ramp it implies.

## Phase 3 — Measure the drift

Diff declared against de-facto:

- **Hardcoded values.** Every raw hex or px in stylesheets, inline styles, or arbitrary-value
  classes (`p-[13px]`) outside the token files — list with `file:line`. Report the ratio of
  token-sourced to hardcoded declarations; colors that resolve from no custom property are the
  same finding seen from the render side.
- **Radius census.** 2–4 related values (4/8/12/full) is a system; 8+ arbitrary values
  (3,5,6,7,10…) is drift — MED, full list quoted.
- **Shadow census.** More than ~5 distinct `box-shadow` strings is drift; shadows with
  inconsistent light direction (some offset up, some down) are a physical-world inconsistency
  a viewer feels without naming — MED, both quoted.
- **Near-duplicate colors.** Pairs within ΔE < 2 both in use (the 14-grays problem). Count
  them; each pair is a merge candidate for Phase 5.
- **Type drift.** Text whose (size, weight, line-height) tuple matches no member of the
  detected scale — list with selectors.

## Phase 4 — Census the components

- **Style signatures.** Group every button by its computed (background, padding, radius,
  font-size, weight) tuple. More than 3–4 signatures for one semantic tier is drift — HIGH;
  two visually different *primary* buttons on one page is HIGH on its own. Repeat for inputs
  and links (underline vs color-only mixed = MED).
- **Icon coherence.** Multiple icon libraries imported (channel A), rendered sizes and stroke
  widths varying within one toolbar (channel B) — MED. Emoji used as icons is a slop tell;
  note it and route to `/designmaxxing:slop`.
- **Component duplication.** Near-duplicate implementations — two Cards, three Modals — via
  similarity search over components (`jscpd` or structural grep). Each duplicate family is a
  MED finding with all paths listed.
- **Theming hygiene.** Dark mode via scattered per-element overrides (`dark:` sprinkled
  through markup) instead of semantic tokens predicts every future drift — LOW now, but name
  the count.

## Phase 5 — Propose the rationalized system

The deliverable that makes this audit actionable. From the censuses, propose the minimal
clean system and the mapping onto it:

```
Grays:   14 in use → 4 proposed   #f4f4f5,#f5f5f5,#fafafa → --surface (#f5f5f6)  [41 usages]
                                   #6b7280,#64748b        → --text-secondary      [78 usages]
Radii:   7 in use → 3 proposed    3px,4px,5px → --radius-sm (4px)                [23 usages]
Shadows: 8 in use → 3 proposed    …
```

Every row: current values, proposed token with name and value, usage count, and the files
touched. Flag any merge that would change a WCAG contrast result — those need a human eye.

## Phase 6 — Report

Use the finding format from `design-review-method` for the drift findings, most severe first,
then the rationalization table. Compute the Consistency category score. Close with **Checked
and clean** (censuses that came back tight) and **Not measured** (routes not censused, whether
the de-facto pass ran).

Then stop. Do not begin fixing. Offer `/designmaxxing:retheme`.

## Recommended next step

Close by printing one line — `→ Recommended next: …` — chosen by what you found:
- Rationalization table produced → `/designmaxxing:retheme` to execute it.
- Drift is mostly one component family → `/designmaxxing:redesign` scoped to that family.
- Declared system is fine, usage is the problem → `/designmaxxing:retheme` in enforce-only mode.
- System healthy → `/designmaxxing:slop` to check it's also *distinctive*, not just consistent.
