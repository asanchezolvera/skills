# DESIGN.md Output Structure Reference

This document defines the exact structure and sections the generated DESIGN.md should follow. The example below uses placeholders — the skill fills them based on the user's interview responses.

The tone should be opinionated and descriptive — not a dry spec sheet. Each section should explain _why_ choices were made, not just _what_ they are. Write as if you're explaining the design system to another designer or engineer who needs to build with it.

---

## Required Sections (in order)

### 1. Visual Theme & Atmosphere

A 2–3 paragraph editorial description of the brand's visual personality. This is the "vibe check" — it should convey the emotional tone, not just list properties. Reference specific hex codes inline when describing colors. End with a **Key Characteristics** list (6–8 bullets) summarizing the defining traits.

### 2. Color Palette & Roles

Organize colors into these sub-groups, giving each color a descriptive name (not just "Primary Blue" — something with personality like "Ocean Deep" or "Midnight Ink"):

- **Primary**: Each of the 3 brand colors is expanded into a full 11-step perceptually balanced palette (generated via `references/palette-generation.md`). For each color, present:
  - A heading with the color's descriptive name, a short role description, and a note about the default (anchor) step
  - A table with columns: Step, Hex, Usage Guidance — covering all 11 steps (50, 100, 200, 300, 400, 500, 600, 700, 800, 900, 950)
  - The anchor step (where the user's original hex sits) is marked with ★ and its usage guidance is bolded as the **default**
  - Each non-anchor step gets a one-line usage note (backgrounds, tints, hover states, pressed states, text-on-brand, dark theme accents, etc.)
  - Component specs later in the document should reference these palette steps for hover/pressed/active states instead of ad-hoc darkened hex values
- **Secondary & Accent (Semantic)**: These are standardized across all Golden Hippo brands — read from `references/semantic-colors.md`. Include the full 50–950 scale for each:
  - Error/Danger: Tailwind Red palette
  - Info: Tailwind Blue palette
  - Success: Tailwind Green palette
  - Warning: Tailwind Yellow palette
    For each palette, include a role mapping table showing which steps map to text, background, and border roles on both light and dark surfaces. Also include Focus Ring blue (blue-500, `#3b82f6`) for accessibility focus states.
- **Surface & Background**: Built from the selected Tailwind neutral palette. Map specific steps to roles:
  - Page background (50 or 100)
  - Card/elevated surface (50 or white)
  - Button/interactive surface (200)
  - Dark surface (800)
  - Deep dark / dark theme bg (900 or 950)
- **Neutrals & Text**: Map neutral palette steps to text hierarchy:
  - Primary text (900 or 950)
  - Secondary text (600 or 700)
  - Tertiary/muted text (500)
  - Text on dark surfaces (200 or 300)
  - Disabled text (400)
- **Semantic & Borders**: Map neutral steps to border/divider roles:
  - Light border (200)
  - Prominent border (300)
  - Dark theme border (700 or 800)
  - Ring/focus shadows (300, 400)
- **Gradient System**: Suggest 1–2 gradients using the brand colors, or note if the brand aesthetic is gradient-free.

### 3. Typography Rules

Based on the user's font selections:

- **Font Family**: Headline, Body/UI, and Code fonts with fallbacks.
- **Hierarchy table**: Display/Hero → Section Heading → Sub-heading (Large, Standard, Small) → Feature Title → Body (Large, Standard, Small, Serif if applicable) → Caption → Label → Overline → Micro → Code. Include: font, size (px and rem), weight, line-height, letter-spacing, and notes.
- **Principles**: 4–5 rules explaining the typographic logic (e.g., weight usage, line-height philosophy, serif/sans split rationale).

### 4. Component Stylings

Concrete specs for:

- **Buttons**: 4–5 variants (primary brand, secondary/neutral, white/light, dark, outline/ghost). Each gets: background, text color, padding, radius, shadow, and a one-line personality description.
- **Cards & Containers**: Surface colors, borders, radius scale, shadow treatment for light and dark contexts.
- **Inputs & Forms**: Text color, padding, border, focus state, radius.
- **Navigation**: Layout approach, colors, border, CTA placement, hover behavior.
- **Image Treatment**: Corner radius, shadow, container approach.
- **Distinctive Components** (optional): Any brand-specific patterns worth calling out.

### 5. Layout Principles

- **Spacing System**: Use the standardized Golden Hippo spacing scale from `references/layout-tokens.md`. List the full scale and call out common usage patterns (component padding, card padding, section spacing, stack gaps, page max-widths).
- **Grid & Container**: Max width, column approach, section layout. Use values from the standardized spacing scale.
- **Whitespace Philosophy**: 2–3 sentences on the spacing personality for this specific brand.
- **Border Radius Scale**: From sharp to maximum rounded, with named levels and use cases. This IS brand-specific — derive it from the brand vibe.

### 6. Depth & Elevation

Use the standardized Golden Hippo shadow scale from `references/layout-tokens.md` (2xs through 2xl). Present as a table mapping each shadow token to its CSS value and typical use case for this brand. Include a **Shadow Philosophy** paragraph explaining how this brand uses the shared shadow scale — which levels are preferred, which are avoided, and why.

### 7. Do's and Don'ts

- **Do** (8–10 items): Specific, actionable rules that reinforce the brand.
- **Don't** (8–10 items): Common mistakes to avoid, referencing specific values.

### 8. Responsive Behavior

- **Breakpoints table**: Use the standardized Golden Hippo breakpoints from `references/layout-tokens.md` (sm: 640px, md: 768px, lg: 1024px, xl: 1280px, 2xl: 1536px). For each breakpoint, describe the key layout changes specific to this brand.
- **Touch Targets**: Minimum sizes and padding.
- **Collapsing Strategy**: How nav, grids, hero, cards, and spacing adapt at each breakpoint.
- **Image Behavior**: Scaling, aspect ratios, art direction notes.

### 9. Agent Prompt Guide

This section is specifically for AI coding assistants. Include:

- **Quick Color Reference**: Include the anchor step plus 2–3 most commonly used adjacent steps for each brand color (e.g., hover, pressed, text-on-tint steps), plus the key neutral and surface colors. Typically 15–20 entries. Mark anchor steps with ★.
- **Example Component Prompts**: 4–5 copy-paste-ready prompts that demonstrate how to ask an AI to build components using this design system. Each prompt should be a complete sentence referencing exact colors (using palette step hex values for hover/pressed states), fonts, sizes, and spacing.
- **Iteration Guide**: 5–7 numbered rules for how to communicate with AI tools about this design system.
