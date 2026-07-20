# Supplement Facts — structural & formatting reference (21 CFR 101.36)

Read this when a label has anything beyond a flat list of nutrients: proprietary
blends, sub-ingredients, dual-serving columns, missing %DV, allergens, or a
structure/function claim. It governs *what elements exist and in what order* —
the look is fixed by `assets/supplement-facts.css`.

## Required elements & order

Inside the box, top to bottom:

1. **"Supplement Facts"** heading — the largest type on the label (the CSS
   already guarantees this; never shrink it).
2. **Serving Size**, then **Servings Per Container** (omit the latter only if it
   genuinely isn't on the source label).
3. **Column headers** — "Amount Per Serving" and "% Daily Value", right-aligned.
4. **Dietary ingredients**, each with its quantitative amount and, where an RDI/DRV
   exists, a **% Daily Value**. Order follows the source label.
5. **Footnotes** — the `†` note and/or the `*` %DV-basis note, if used.

Outside the box, below it, in this order:

6. **Other Ingredients:** list (non-dietary: fillers, capsule material, flow
   agents), in descending order by weight.
7. **Contains:** allergen declaration (FALCPA), if present.
8. **DSHEA disclaimer** ("These statements have not been evaluated…"), if the
   label carries any structure/function claim.

Other Ingredients, allergen, and disclaimer are **not** part of the boxed panel —
keep them as `<p>` elements after `</table>`, never as table rows. Trademark /
registered-mark attribution lines (e.g. "X® is a registered mark of …") go last,
as a `<p class="supp-facts__note">` — smaller small-print below everything else.

## Footnote symbols

Carry whatever symbols the source label uses — labels vary (`†`, `*`, `‡`, `**`,
etc.). Each symbol that appears in a row must have a matching footnote line, and
vice-versa. Common ones:

- **"Daily Value not established"** — for any ingredient with no RDI/DRV (most
  botanicals, amino acids, proprietary blends). Put the symbol (often `&dagger;`)
  in that row's %DV cell and include its footnote once.
- **"% Daily Values are based on a 2,000 calorie diet"** — include only if the
  source has it. Some labels mark this with `*`, others with `&Dagger;` (‡).
- **Manufacturing / potency notes** (e.g. `**` "At time of manufacture") — carry
  them verbatim, in both the value cell and the footnote list.

Put all footnote lines in the single `<tfoot>` cell, each as its own `<p>`. If a
label has no footnotes at all, emit no `<tfoot>` and put the `--last` heavy rule
on the final ingredient row instead. Preserve `< 1%` values verbatim
(`&lt; 1%`); never round to `0%`.

## Proprietary / named blends

A blend is a **single row**, not a header plus sub-rows. In the name cell:
`<span class="supp-facts__blend-name">` for the bold blend name, then
`<span class="supp-facts__blend-list">` listing the component ingredients inline
(comma-separated, in source order — descending by weight). The row carries the
blend's **total weight** in the amount cell and its footnote symbol (usually
`&dagger;`) in the %DV cell. Add `supp-facts__row--blend` so the row-header stops
being bold (the blend name span provides the bold). Never invent per-component
amounts the label doesn't disclose.

Use the heavy 5px separators to bracket a blend section: `supp-facts__row--rule-top`
on the first blend row and `supp-facts__row--last` on the last row before the
footnotes (a single blend row that both opens and closes the section takes both).

## Amounts & units

- Keep the label's own units and spelling: `mcg`, `mg`, `g`, `IU`, `mcg DFE`,
  `mg NE`, etc. Don't convert.
- Vitamin subscripts (D₃, B₁₂) → `<sub>` (e.g. `Vitamin B<sub>12</sub>`).
- "as ____" source parentheticals stay on the ingredient name line.

## Dual-serving / two-column labels

Some labels show two amount columns (e.g. "Per Capsule" and "Per 2 Capsules").
Add a second amount `<td>` per row and a matching `<th scope="col">` header, keep
one %DV column at the end, and set every row's cells consistently. Note this in
the summary so the person can confirm the extra column rendered correctly.

## When the source is missing something

Render only what the label actually states. If a required element is absent from
the source (e.g. no Servings Per Container, no allergen line), omit it and flag
it in the one-line summary rather than fabricating a value — the person owns the
compliance decision, not the skill.
