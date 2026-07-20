# `design-system-stylesheet`

Generate a visual style sheet from an existing `DESIGN.md` — a single-page HTML file that renders the design system as a living document.

The skill parses a `DESIGN.md` and produces a self-contained HTML file with embedded CSS and JavaScript. The page uses the brand's own design tokens to style itself, making the style sheet both documentation and proof of the design system.

## Rendered sections

1. **Header** — Brand name and subtitle, styled with primary color and brand fonts
2. **Color Palette** — Brand color cards, neutral palette strip, and semantic palette strips with automatic light/dark text contrast
3. **Typography Scale** — Live text specimens at every hierarchy level (Display through Micro and Code) with exact sizes, weights, and spacing
4. **Spacing Scale** — Horizontal bar chart of all spacing values
5. **Shadow & Radius Scales** — Card specimens demonstrating each shadow token and border radius level
6. **Component Specimens** — Buttons in 4 states (default, hover, focus, active), inputs in 4 states (empty, filled, focused, disabled), and card layouts
7. **Footer** — Generation metadata

## Technical details

- All tokens defined as CSS custom properties on `:root`
- JavaScript contrast utility for automatic light/dark text on color swatches
- Google Fonts loaded via `@import`
- All interaction states rendered statically (no CSS pseudo-classes) so every state is visible at a glance
- Desktop-focused layout (1024px+), max-width 1280px

## Output

A single `STYLE-SHEET.html` file.

---

Consumes the `DESIGN.md` produced by [`create-design-md`](../create-design-md/). See the [repo README](../README.md) for the full skill index.
