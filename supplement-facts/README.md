# `supplement-facts`

Turn a supplement label — supplied as raw HTML **or** as an image/photo/screenshot — into one self-contained block of semantic HTML + CSS, ready to paste onto a product page or into a CMS (e.g. Builder.io).

Every label the skill produces looks identical because they all carry the same canonical scoped stylesheet, which is emitted verbatim and never restyled per label. That shared consistency is the point.

## Output characteristics

- Faithful to the FDA Supplement Facts structure (21 CFR 101.36)
- A real accessible `<table>` — `<caption>`, `<thead>/<tbody>/<tfoot>`, `<th scope="row">` on nutrient names — no `<div>` soup
- Responsive: fluid widths, `clamp()` type, reflows on narrow screens
- Scoped CSS that can't collide with the host page
- Other Ingredients, allergen ("Contains:"), and the DSHEA disclaimer rendered as paragraphs outside the panel, never as table rows

For multiple labels on one page, the `<style>` block is included once and only the `.supp-facts` markup is repeated.

## Workflow

1. **Read the input** — Transcribe every element from the image, or parse the same fields out of existing markup (re-expressing the *data*, not preserving the source's tags). Units, ordering, symbols, `<1%` values, and "as ___" source notes are preserved exactly; nothing is inferred that isn't printed.
2. **Map to the canonical markup** — Follow `assets/template.html` exactly.
3. **Assemble** — A single `<style>` block with the full unmodified CSS, followed by the `.supp-facts` markup.
4. **Verify** — Structure, CSS consistency, completeness, accessibility, and responsiveness are each checked before delivery; anything missing from the *source* is flagged, not invented.

## Scope

The skill structures and styles the data it is given. It does **not** verify regulatory compliance, calculate %DV, or substantiate a formula's claims — it renders faithfully and surfaces gaps so the person owns the compliance call.

## Files

| File | Purpose |
|------|---------|
| [`assets/supplement-facts.css`](./assets/supplement-facts.css) | The canonical stylesheet — single source of truth for the look, emitted verbatim |
| [`assets/template.html`](./assets/template.html) | The exact semantic markup to reproduce, with a worked example (proprietary blend, footnotes, allergen, disclaimer) |
| [`references/fda-format.md`](./references/fda-format.md) | Element ordering, footnote symbols, blends, dual-serving columns, unit handling |

---

See the [repo README](../README.md) for the full skill index.
