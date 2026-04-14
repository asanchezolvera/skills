---
name: design-system-stylesheet
description: Generate a visual style sheet from a DESIGN.md file — a single-page HTML artifact that renders color cards, typography scales, spacing visualization, and interactive component specimens (buttons, inputs, etc.) using the brand's own design tokens. Use this skill whenever the user wants to visualize a design system, create a style guide page, build a component showcase, render a design spec as a living page, or turn a DESIGN.md into something visual. Also trigger when the user says things like "show me what this design system looks like", "build a style sheet from my DESIGN.md", "create a component preview", or "visualize my design tokens". This skill requires a DESIGN.md file as input — if one isn't provided, ask for it.
---

# Design System Style Sheet Generator

Generate a single-page HTML artifact that visually renders a DESIGN.md as a living style sheet. The page uses the brand's own design tokens (colors, fonts, spacing, shadows, radii) to style itself — the style sheet is both the documentation and the proof.

## Input

The skill expects a DESIGN.md file uploaded by the user. Check `/mnt/user-data/uploads/` for the file. If no DESIGN.md is found, ask the user to upload one.

Read the entire DESIGN.md carefully before generating any code. You need to extract:

1. **Brand colors** — primary, secondary, tertiary (names + hex values)
2. **Neutral palette** — which Tailwind neutral was selected, all 50–950 steps with hex values
3. **Semantic palettes** — error (red), info (blue), success (green), warning (yellow) with their step/hex values
4. **Typography** — font families (headline, body, code), the full hierarchy table (role, font, size, weight, line-height, letter-spacing)
5. **Spacing scale** — the full list of spacing values
6. **Shadow scale** — all named shadow tokens with their CSS values
7. **Border radius scale** — all named levels with px values
8. **Component specs** — button variants (background, text, padding, radius, shadow, hover state), input specs, card specs
9. **Surface colors** — page background, card surface, dark surface, etc.

Be thorough. Missing a color name or misreading a hex value will make the style sheet inconsistent with the design system. If something is ambiguous in the DESIGN.md, make a reasonable inference based on the palette and note the assumption.

## Output Structure

Generate a single React (.jsx) file. The page is a continuous vertical scroll with clearly separated sections. Each section has a header and visual specimens.

The page must use the brand's own tokens for its own styling:

- Page background = the DESIGN.md's page background color
- Section headers = the headline font at the appropriate hierarchy level
- Body text = the body font
- Borders, shadows, radii = from the DESIGN.md's token scales

### Section 1: Header

A simple branded header with:

- The brand/project name as a Display-level heading
- A subtitle: "Design System Style Sheet"
- Styled using the brand's primary color and page background

### Section 2: Color Palette

Render color cards for each color group. Each card shows:

- A swatch rectangle (minimum 80px tall) filled with the color
- The color's descriptive name (e.g., "Coach Blue", "Stone 500")
- The hex value
- A light/dark text sample on the swatch to demonstrate contrast

Organize into sub-sections matching the DESIGN.md structure:

**Brand Colors** — primary, secondary, tertiary as large cards (side by side)

**Neutral Palette** — all 50–950 steps as a horizontal strip of connected swatches (like a Tailwind palette strip). Each swatch shows the step number and hex. Text color on each swatch should automatically flip between light and dark for readability (light text on dark swatches, dark text on light swatches).

**Semantic Palettes** — error, info, success, warning. Each as its own horizontal strip, same format as the neutral palette.

### Section 3: Typography Scale

Render every level from the typography hierarchy table as a live specimen. For each level show:

- The role name as a label (e.g., "Display / Hero", "Body Standard")
- A sample text string rendered at that exact size, weight, line-height, and letter-spacing using the correct font family
- Metadata underneath: font family, size, weight, line-height, letter-spacing — rendered in the body caption style

Use a meaningful sample string, not "Lorem ipsum." Something like the brand name or a short phrase that feels natural.

### Section 4: Spacing Scale

Render the spacing scale as a visual bar chart. For each spacing value:

- A horizontal bar whose width equals the spacing value (cap the visual width for very large values — anything above 128px can be rendered at a reduced scale with the actual value labeled)
- The pixel value labeled next to the bar

Use the brand's primary or secondary color for the bars, with the neutral palette for labels.

### Section 5: Shadow & Radius Scales

**Shadows**: Render a row of identical cards (same size, same background), each applying a different shadow token. Label each card with the token name (2xs, xs, sm, md, lg, xl, 2xl).

**Border Radius**: Render a row of identical filled rectangles, each with a different border-radius value. Label each with the named level and px value.

### Section 6: Component Specimens

Render interactive components from the DESIGN.md's component specs.

**Buttons**: Render every button variant from the spec. Each button should:

- Use the exact background, text color, padding, radius, and shadow from the DESIGN.md
- Show four states side by side: default, hover, focus, and active/pressed
- Have a label underneath with the variant name

To show all states alongside default, render four instances of each button: one in default state, one with the hover styles applied directly via inline styles, one with the focus ring/outline applied statically, and one with the active/pressed styles. Label them "Default", "Hover", "Focus", "Active".

The focus state should show the DESIGN.md's focus ring treatment (typically a `2px solid` border in the focus ring color with an outer glow/ring). If the DESIGN.md specifies a specific focus ring color (usually blue-500 `#3b82f6`), use that. The focus state is critical for accessibility documentation — don't skip it.

**Inputs**: Render input fields showing:

- Empty state with placeholder text
- Filled state with sample text
- Focused state (with the focus ring/border applied statically)
- Disabled state
- Error state (if error styling is specified)

**Cards**: Render 1–2 card specimens showing:

- The card surface, border, radius, and shadow from the spec
- A sample layout inside (heading + body text + a button) using the typography hierarchy
- If the DESIGN.md specifies distinctive card variants (like the Functiondex workout card with left-side color bar), render those too

### Section 7: Footer

A minimal footer with:

- "Generated from DESIGN.md"
- The current date
- Styled in the caption/meta text style

## Code Guidelines

**Single HTML file with embedded CSS and JavaScript.** All HTML, CSS, and JS live in one `.html` file. Define the brand's design tokens as CSS custom properties (variables) on `:root` so they're referenceable throughout the stylesheet. Use semantic HTML where possible (`<section>`, `<h2>`, `<figure>`, etc.).

**CSS structure**: Define all token values (colors, fonts, shadows, radii, spacing) as CSS custom properties at the top. Use these variables throughout rather than hardcoding hex values in individual rules. This makes the style sheet itself a reference for the token names.

```css
:root {
  --color-primary: #6366f1;
  --color-surface-canvas: #f8fafc;
  --shadow-xs: 0 1px 2px 0 rgb(0 0 0 / 0.05);
  --font-headline: "Satoshi", Inter, system-ui, sans-serif;
  /* ... */
}
```

**Font loading**: For Google Fonts or Fontshare, use `@import` in a `<style>` tag. For system fonts (system-ui, Georgia, etc.), no import needed. For custom/proprietary fonts, fall back to the specified fallback stack.

**Contrast logic**: Include a small JavaScript utility that takes a hex color and returns whether to use light or dark text on it. Use this for color swatch labels:

```js
function getContrastText(hex) {
  const r = parseInt(hex.slice(1, 3), 16);
  const g = parseInt(hex.slice(3, 5), 16);
  const b = parseInt(hex.slice(5, 7), 16);
  const luminance = (0.299 * r + 0.587 * g + 0.114 * b) / 255;
  return luminance > 0.5 ? "#000000" : "#ffffff";
}
```

**Responsive**: The style sheet should be usable on desktop screens (1024px+). It doesn't need to be fully mobile-responsive — it's a reference document, not a consumer product. Use a max-width container (1280px) centered on the page.

**Interactive states**: For buttons and inputs, render multiple instances showing each state statically applied rather than relying on CSS :hover/:focus pseudo-classes. This way every state (including focus rings) is visible at a glance without user interaction.

## Output

1. Write the complete .html file to `/mnt/user-data/outputs/STYLE-SHEET.html`
2. Present the file to the user using `present_files`
3. Follow up with a brief note (2–3 sentences) on what was generated and any assumptions made
