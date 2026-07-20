---
name: supplement-facts
description: >-
  Generate a styled, accessible, responsive HTML Supplement Facts label from
  either raw HTML markup or an image/photo/screenshot of a supplement label.
  Use this whenever someone provides a supplement label — as an image or as
  existing markup — and wants clean web-ready HTML for it, or mentions
  "supplement facts", "nutrition panel", "supplement label", "convert this
  label to HTML", "restyle this supplement panel", or wants a supplement label
  to drop onto a product page or CMS (e.g. Builder.io). Always use this skill
  for that task rather than hand-writing a one-off panel, so every label across
  the site shares one consistent, compliant, accessible style.
---

# Supplement Facts label generator

Turn a supplement label (raw HTML **or** an image) into one self-contained block
of semantic HTML + CSS, ready to paste onto a website. Every label this skill
produces looks identical because they all carry the same scoped stylesheet —
that shared consistency is the whole point, so the canonical CSS is never edited
per label.

The output is faithful to the FDA Supplement Facts structure (21 CFR 101.36),
uses a real accessible `<table>`, and is responsive (a fluid, wide web treatment).

## Files

- `assets/supplement-facts.css` — the canonical stylesheet, the single source of
  truth for the look. Emit it **verbatim**; never restyle it for an individual
  label.
- `assets/template.html` — the exact semantic markup to reproduce, with a worked
  example (proprietary blend with inline components, footnotes, allergen, disclaimer).
- `references/fda-format.md` — element ordering, footnote symbols, blends,
  dual-serving columns, unit handling. **Read it** whenever the label is anything
  more than a flat list of nutrients.

## Workflow

### 1. Read the input

- **Image / photo / screenshot** → read the label visually and transcribe every
  element: the title, serving size, servings per container, each ingredient with
  its amount and %DV (preserve `<1%`, units, and "as ___" source notes exactly),
  proprietary blends and their component lists, footnote symbols, Other
  Ingredients, allergen ("Contains:") line, DSHEA disclaimer, and any trademark
  attribution lines. Preserve the label's own column wording (e.g. "% DV**"). Transcribe
  what is actually printed — don't infer amounts or %DV values that aren't shown.
- **Raw HTML** → parse out the same fields from the existing markup, ignoring its
  original styling and structure. You're re-expressing the *data* in the
  canonical form, not preserving the source's tags.

If the label is more than a simple nutrient list, read `references/fda-format.md`
before mapping.

### 2. Map to the canonical markup

Follow `assets/template.html` exactly:

- `.supp-facts` > `.supp-facts__panel` (the box) > `<table>`.
- `<caption class="supp-facts__title">` for "Supplement Facts" (this names the
  table for screen readers — keep it even though it renders as the big heading).
- `<colgroup>` with the 57% / 25% / 18% widths.
- `<thead>`: each serving line as a `supp-facts__serving` row —
  `<th colspan="3" scope="colgroup">` with a bold `<strong>` label and colon
  (e.g. `<strong>Serving Size:</strong> 1 Scoop (4 g)`). Mark the last serving
  row `supp-facts__serving--last`. Then the `supp-facts__colhead` row: an empty
  name header carrying `<span class="supp-facts__sr-only">Nutrient</span>`, then
  `supp-facts__amount` "Amount Per Serving" and `supp-facts__dv` "% Daily Value".
- `<tbody>`: one `supp-facts__row` per ingredient — `<th scope="row">` for the
  name, `<td class="supp-facts__amount">` for the amount, `<td class="supp-facts__dv">`
  for the %DV (its footnote symbol where no DV is established). Amounts and %DV
  are right-aligned and bold via those classes. A named/proprietary blend is one
  row with `supp-facts__row--blend` (see `references/fda-format.md`).
- `<tfoot>`: a single `supp-facts__foot` row, `<td colspan="3">` holding each
  footnote as its own `<p>`. Omit `<tfoot>` if the label has none.
- After `</table>` (outside `.supp-facts__panel`): `Other Ingredients`,
  `Contains` (allergen), and the DSHEA disclaimer as `<p>` elements — **never**
  as table rows.

Preserve the label's own units, ordering, symbols, and wording. Use `<sub>` for
vitamin subscripts (D₃, B₁₂), `<em>` for Latin botanical names, and carry `™`/`®`
as written.

### 3. Assemble the output

Emit, in order:

1. A single `<style>` block containing the **full, unmodified** contents of
   `assets/supplement-facts.css`.
2. The `.supp-facts` markup from step 2.

This block is self-contained — it renders identically wherever it's pasted, and
its scoping means it can't collide with the host page's CSS. Deliver it as a code
file/artifact the person can copy, not just inline prose.

Mention once, briefly, that for multiple labels on the same page they can include
the `<style>` block a single time and paste only the `.supp-facts` markup for the
rest.

### 4. Verify before delivering

Check every item — these are the skill's non-negotiables:

- **Structure**: real `<table>` with `<caption>`, `<thead>/<tbody>/<tfoot>`,
  `<th scope="row">` on names, `<th scope="col">` on column headers. No `<div>`
  soup, no layout tables-within-tables.
- **Consistency**: the CSS is byte-for-byte the canonical file; nothing was
  tweaked for this label.
- **Completeness**: every ingredient, amount, %DV, footnote, Other Ingredient,
  allergen, and disclaimer from the source is present and correctly ordered. Any
  element missing from the *source* is flagged, not invented.
- **Accessibility**: title is the largest type; contrast is the default black-on-
  white; meaning never depends on color alone; type is clamp()/relative so zoom
  and font-size preferences work.
- **Responsive**: no fixed pixel widths added to the panel; it fills its
  container up to the stylesheet's cap and reflows on narrow screens.

Close with a one-line summary of what was rendered and anything the source label
was missing (so the person owns the compliance call).

## Scope

This skill styles and structures a label from data the person supplies. It does
**not** verify regulatory compliance, calculate %DV, or validate that a formula's
claims are substantiated — it faithfully renders what it's given and surfaces
gaps. If asked to judge compliance, say so and point to the source data.
