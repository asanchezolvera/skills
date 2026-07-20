# `create-design-md`

Create a comprehensive `DESIGN.md` design system document through a guided interview. Output conforms to the [Google DESIGN.md spec](https://github.com/google-labs-code/design.md) (YAML front matter + canonical section order) and is lint-validated with `@google/design.md` after generation.

The skill walks the user through a 6-step conversational interview to collect brand information, then produces a 10-section design system document covering visual theme, colors, typography, layout, elevation, shapes, components, do's/don'ts, responsive behavior, and an AI agent prompt guide.

## Interview steps

1. **Brand Foundation** — The user describes their brand, product, and audience in a few sentences (also becomes the `description` in the YAML front matter)
2. **Brand Colors** — Three hex codes: primary, secondary, and tertiary
3. **Neutral Palette** — Two-step picker: first the family (Classic vs Tinted v4.2), then the specific palette
   - **Classic**: Stone, Gray, Neutral, Zinc (Slate offered via follow-up)
   - **Tinted (v4.2)**: Taupe, Mauve, Mist, Olive
4. **Typography** — Font preferences for headlines, body text, and code
5. **Brand Vibe** — Multi-select aesthetic tags (Minimal, Bold, Warm, Cool, Playful, Luxurious, Developer-focused, Editorial)
6. **Confirm & Generate** — Summary and confirmation before generation

## Palette generation

Each brand color is expanded into an 11-step perceptually balanced palette (50–950) using the OKLCH color space. The user's original hex is preserved exactly at its anchor step, and adjacent steps are derived using the `coloraide` Python library. High-chroma colors that anchor at light steps are nudged 1–2 steps darker so the three brand palettes read evenly together.

## Output structure

A `DESIGN.md` file with YAML front matter (machine-readable token block) followed by 10 prose sections in the spec's canonical order:

1. Visual Theme & Atmosphere (Overview)
2. Color Palette & Roles (Colors)
3. Typography Rules (Typography)
4. Layout Principles (Layout)
5. Depth & Elevation (Elevation & Depth)
6. Shapes
7. Component Stylings (Components)
8. Do's and Don'ts
9. Responsive Behavior (Golden Hippo extension)
10. Agent Prompt Guide (Golden Hippo extension)

**Standardized tokens** (shared across all Golden Hippo Labs brands and not customized per-brand): semantic palettes (Tailwind Red/Blue/Green/Yellow for error/info/success/warning), the spacing scale (0–1920px), the shadow scale (`2xs` through `2xl`), and breakpoints (sm/md/lg/xl/2xl).

## Validation

The generated file is linted with `npx @google/design.md lint`. Target outcome is 0 errors and 0 `contrast-ratio` / `section-order` warnings; component `backgroundColor`/`textColor` pairs are bumped to darker/lighter palette steps until they clear WCAG AA at 4.5:1.

## References

| File | Purpose |
|------|---------|
| [`references/output-structure.md`](./references/output-structure.md) | Template for the 10-section DESIGN.md prose structure, in canonical spec order |
| [`references/yaml-frontmatter.md`](./references/yaml-frontmatter.md) | YAML front matter token schema and component property rules (closed property set per the spec) |
| [`references/palette-generation.md`](./references/palette-generation.md) | OKLCH-based algorithm for expanding brand colors into 11-step palettes |
| [`references/semantic-colors.md`](./references/semantic-colors.md) | Standardized error/info/success/warning palettes (Tailwind Red/Blue/Green/Yellow) |
| [`references/layout-tokens.md`](./references/layout-tokens.md) | Standardized spacing scale, shadow scale, and breakpoints |
| [`references/tailwind-neutrals.md`](./references/tailwind-neutrals.md) | Nine Tailwind neutral palette options (five classic + four v4.2 tinted) with hex values and personality notes |

---

Pairs with [`design-system-stylesheet`](../design-system-stylesheet/), which renders the generated `DESIGN.md` as a visual style sheet. See the [repo README](../README.md) for the full skill index.
