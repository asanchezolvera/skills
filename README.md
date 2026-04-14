# Skills

A collection of agent skills that extend capabilities across planning, development, and tooling. Built for **Golden Hippo's** suite of brands.

---

## Skills

### [`design-system-generator`](./design-system-generator/)

Generate a comprehensive `DESIGN.md` design system document through a guided interview.

The skill walks the user through a 6-step conversational interview to collect brand information, then produces a detailed 9-section design system document covering visual theme, color palettes, typography, component specs, layout, elevation, do's/don'ts, responsive behavior, and an AI agent prompt guide.

**Interview steps:**

1. **Brand Foundation** — The user describes their brand, product, and audience in a few sentences
2. **Brand Colors** — Three hex codes: primary, secondary, and tertiary
3. **Neutral Palette** — Selection from five Tailwind neutral options (Stone, Gray, Neutral, Zinc, Slate)
4. **Typography** — Font preferences for headlines, body text, and code
5. **Brand Vibe** — Multi-select aesthetic tags (Minimal, Bold, Warm, Cool, Playful, Luxurious, Developer-focused, Editorial)
6. **Confirm & Generate** — Summary and confirmation before generation

**Palette generation:** Each brand color is expanded into an 11-step perceptually balanced palette (50–950) using the OKLCH color space. The user's original hex is preserved exactly at its anchor step, and adjacent steps are derived using the `coloraide` Python library.

**Output:** A `DESIGN.md` file with 9 standardized sections.

#### References

| File | Purpose |
|------|---------|
| [`references/output-structure.md`](./design-system-generator/references/output-structure.md) | Template for the 9-section DESIGN.md structure |
| [`references/palette-generation.md`](./design-system-generator/references/palette-generation.md) | OKLCH-based algorithm for expanding brand colors into 11-step palettes |
| [`references/semantic-colors.md`](./design-system-generator/references/semantic-colors.md) | Standardized error/info/success/warning palettes (Tailwind Red/Blue/Green/Yellow) |
| [`references/layout-tokens.md`](./design-system-generator/references/layout-tokens.md) | Standardized spacing scale, shadow scale, and breakpoints |
| [`references/tailwind-neutrals.md`](./design-system-generator/references/tailwind-neutrals.md) | Five Tailwind neutral palette options with hex values and personality notes |

---

### [`design-system-stylesheet`](./design-system-stylesheet/)

Generate a visual style sheet from an existing `DESIGN.md` — a single-page HTML file that renders the design system as a living document.

The skill parses a `DESIGN.md` and produces a self-contained HTML file with embedded CSS and JavaScript. The page uses the brand's own design tokens to style itself, making the style sheet both documentation and proof of the design system.

**Rendered sections:**

1. **Header** — Brand name and subtitle, styled with primary color and brand fonts
2. **Color Palette** — Brand color cards, neutral palette strip, and semantic palette strips with automatic light/dark text contrast
3. **Typography Scale** — Live text specimens at every hierarchy level (Display through Micro and Code) with exact sizes, weights, and spacing
4. **Spacing Scale** — Horizontal bar chart of all spacing values
5. **Shadow & Radius Scales** — Card specimens demonstrating each shadow token and border radius level
6. **Component Specimens** — Buttons in 4 states (default, hover, focus, active), inputs in 4 states (empty, filled, focused, disabled), and card layouts
7. **Footer** — Generation metadata

**Technical details:**

- All tokens defined as CSS custom properties on `:root`
- JavaScript contrast utility for automatic light/dark text on color swatches
- Google Fonts loaded via `@import`
- All interaction states rendered statically (no CSS pseudo-classes) so every state is visible at a glance
- Desktop-focused layout (1024px+), max-width 1280px

**Output:** A single `STYLE-SHEET.html` file.

---

## Workflow

The two skills are designed to be used in sequence:

```
design-system-generator          design-system-stylesheet
        │                                │
        ▼                                ▼
  User interview  ──→  DESIGN.md  ──→  STYLE-SHEET.html
  (6 steps)           (9 sections)      (visual rendering)
```

1. Run the **design-system-generator** skill to interview the user and produce a `DESIGN.md`
2. Run the **design-system-stylesheet** skill to visualize that `DESIGN.md` as an interactive HTML page

---

## Skill Format

Each skill is a directory containing a `SKILL.md` file with YAML frontmatter:

```yaml
---
name: skill-name
description: Trigger phrases and description of when the skill should activate
---
```

The Markdown body defines the skill's workflow, rules, and generation guidelines. Skills may include a `references/` directory with supplementary data files.
