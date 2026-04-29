# DESIGN.md Output Structure Reference

This document defines the exact structure and sections the generated DESIGN.md should follow. The output conforms to the [Google DESIGN.md spec](https://github.com/google-labs-code/design.md) — meaning it begins with a YAML front matter token block, uses the canonical section order, and uses the spec's component property names so it can be linted, diffed, and exported by `@google/design.md`.

The tone of the prose sections should be opinionated and descriptive — not a dry spec sheet. Each section should explain _why_ choices were made, not just _what_ they are. Write as if you're explaining the design system to another designer or engineer who needs to build with it.

---

## Document Structure

```
---
<YAML front matter — see references/yaml-frontmatter.md>
---

# <Brand Name>

## 1. Visual Theme & Atmosphere   (spec section: Overview)
## 2. Color Palette & Roles       (spec section: Colors)
## 3. Typography Rules            (spec section: Typography)
## 4. Layout Principles           (spec section: Layout)
## 5. Depth & Elevation           (spec section: Elevation & Depth)
## 6. Shapes                      (spec section: Shapes)
## 7. Component Stylings          (spec section: Components)
## 8. Do's and Don'ts             (spec section: Do's and Don'ts)
## 9. Responsive Behavior         (extension — preserved by spec)
## 10. Agent Prompt Guide         (extension — preserved by spec)
```

The first 8 sections match the canonical spec order. Sections 9 and 10 are Golden Hippo-specific extensions; the spec preserves unknown sections without erroring, so they're safe to include.

The H2 heading text can be the descriptive titles above (e.g., "Visual Theme & Atmosphere"); the spec recognizes "Brand & Style" as an alias for "Overview", "Layout & Spacing" for "Layout", and "Elevation" for "Elevation & Depth". Either form passes lint.

---

## Front Matter (Required, First)

Before any prose, emit a complete YAML front matter block. See `references/yaml-frontmatter.md` for the full token schema, naming conventions, and component property rules.

The front matter MUST include:

- `version: alpha`, `name`, `description`
- `colors` — full 11-step palettes for primary/secondary/tertiary, the selected neutral palette, the four standardized semantic palettes, and semantic aliases (`primary`, `surface`, `on-surface`, etc.)
- `typography` — every level the prose hierarchy defines
- `rounded` — the brand-specific radius scale
- `spacing` — the standardized Golden Hippo scale
- `components` — every component the prose specs, using token references and the spec's closed property set

Component definitions use `{colors.primary-600}` references, NOT raw hex codes. This makes the Tailwind export round-trip cleanly.

---

## Required Prose Sections (in order)

### 1. Visual Theme & Atmosphere

_(maps to spec's "Overview" section)_

A 2–3 paragraph editorial description of the brand's visual personality. This is the "vibe check" — it should convey the emotional tone, not just list properties. Reference specific hex codes inline when describing colors. End with a **Key Characteristics** list (6–8 bullets) summarizing the defining traits.

### 2. Color Palette & Roles

_(maps to spec's "Colors" section)_

Organize colors into these sub-groups, giving each color a descriptive name (not just "Primary Blue" — something with personality like "Ocean Deep" or "Midnight Ink"):

- **Primary**: Each of the 3 brand colors is expanded into a full 11-step perceptually balanced palette (generated via `references/palette-generation.md`). For each color, present:
  - A heading with the color's descriptive name, a short role description, and a note about the default (anchor) step
  - A table with columns: Step, Token, Hex, Usage Guidance — covering all 11 steps (50, 100, 200, 300, 400, 500, 600, 700, 800, 900, 950). The Token column shows the YAML name (e.g., `primary-500`) so engineers can map prose directly to front matter.
  - The anchor step (where the user's original hex sits) is marked with ★ and its usage guidance is bolded as the **default**
  - Each non-anchor step gets a one-line usage note (backgrounds, tints, hover states, pressed states, text-on-brand, dark theme accents, etc.)
  - Component specs later in the document should reference these palette steps for hover/pressed/active states instead of ad-hoc darkened hex values
- **Secondary & Accent (Semantic)**: Standardized across all Golden Hippo brands — read from `references/semantic-colors.md`. Include the full 50–950 scale for each:
  - Error/Danger: Tailwind Red palette
  - Info: Tailwind Blue palette
  - Success: Tailwind Green palette
  - Warning: Tailwind Yellow palette
    For each palette, include a role mapping table showing which steps map to text, background, and border roles on both light and dark surfaces. Also include Focus Ring blue (blue-500, `#3b82f6`) for accessibility focus states.
- **Surface & Background**: The default page background is **pure white (`#ffffff`)** unless there's a specific reason to deviate (e.g., a dark-by-default brand, an editorial brand whose entire aesthetic depends on a tinted page, or the user explicitly asks for a tinted background during the interview). White as the page surface keeps content legible, lets the neutral palette do its job in cards/borders/text, and makes the brand colors land cleanly. Use the selected Tailwind neutral palette for everything _adjacent_ to the page surface — cards, dividers, recessed sections, etc. Map specific steps to roles:
  - Page background — **`#ffffff`** (white) by default
  - Card/elevated surface — `neutral-50` for visible-on-white separation, OR `#ffffff` itself with a `neutral-200` border (pick whichever fits the brand's depth philosophy)
  - Recessed/secondary surface — `neutral-50` or `neutral-100` (the level below page when page is white)
  - Button/interactive surface — `neutral-100` or `neutral-200`
  - Dark surface — `neutral-800`
  - Deep dark / dark theme bg — `neutral-900` or `neutral-950`

  If the brand's vibe genuinely calls for a tinted page background (warm editorial, moody dark-mode-first, etc.), justify the choice in 1–2 sentences in the prose and use a specific neutral step rather than defaulting. Don't tint the page just because the neutral palette has a nice 50.

- **Neutrals & Text**: Map neutral palette steps to text hierarchy:
  - Primary text (900 or 950)
  - Secondary text (600 or 700)
  - Tertiary/muted text (500)
  - Text on dark surfaces (200 or 300)
  - Disabled text (400)
- **Borders & Dividers**: Map neutral steps to border/divider roles:
  - Light border (200)
  - Prominent border (300)
  - Dark theme border (700 or 800)
  - Ring/focus shadows (300, 400)
- **Gradient System**: Suggest 1–2 gradients using the brand colors, or note if the brand aesthetic is gradient-free.

### 3. Typography Rules

_(maps to spec's "Typography" section)_

Based on the user's font selections:

- **Font Family**: Headline, Body/UI, and Code fonts with fallbacks.
- **Hierarchy table**: Display/Hero → Section Heading → Sub-heading (Large, Standard, Small) → Feature Title → Body (Large, Standard, Small, Serif if applicable) → Caption → Label → Overline → Micro → Code. Include columns: Token, Font, Size (px and rem), Weight, Line-height, Letter-spacing, Notes. The Token column shows the YAML name (e.g., `body-md`) for direct mapping to front matter.
- **Principles**: 4–5 rules explaining the typographic logic (e.g., weight usage, line-height philosophy, serif/sans split rationale).

### 4. Layout Principles

_(maps to spec's "Layout" section)_

- **Spacing System**: Use the standardized Golden Hippo spacing scale from `references/layout-tokens.md`. List the full scale and call out common usage patterns (component padding, card padding, section spacing, stack gaps, page max-widths).
- **Grid & Container**: Max width, column approach, section layout. Use values from the standardized spacing scale.
- **Whitespace Philosophy**: 2–3 sentences on the spacing personality for this specific brand.

> Border radius is now in its own **Shapes** section per the spec — do NOT include a radius scale here.

### 5. Depth & Elevation

_(maps to spec's "Elevation & Depth" section)_

Use the standardized Golden Hippo shadow scale from `references/layout-tokens.md` (2xs through 2xl). Present as a table mapping each shadow token to its CSS value and typical use case for this brand. Include a **Shadow Philosophy** paragraph explaining how this brand uses the shared shadow scale — which levels are preferred, which are avoided, and why.

For flat / shadow-averse brands, this section should still exist but explain the alternative depth strategies (borders, tonal layers, color contrast) the brand uses instead. The spec explicitly calls out this case.

### 6. Shapes

_(maps to spec's "Shapes" section — NEW, split out from Layout)_

This section defines how visual elements are shaped. The token block in front matter uses the `rounded` map; this prose section explains the rationale.

- **Shape Language**: 1–2 sentences on the brand's overall geometric posture (sharp/architectural, soft/approachable, fully rounded/playful, mixed).
- **Border Radius Scale**: Table with columns Token (e.g., `rounded.sm`), Value, Use Case. Cover at minimum: `none`, `sm`, `md`, `lg`, `xl`, `full`. Adjust the values to fit the brand vibe — sharp/architectural brands stay at 0–4px, soft/friendly brands push 8–16px+, playful brands may use larger values.
- **Application Rules**: Which radius levels apply to which component types (buttons, cards, inputs, images, badges, modals).

### 7. Component Stylings

_(maps to spec's "Components" section)_

Concrete specs for each component below. Every component must also be defined in the front matter `components` block using token references (`{colors.primary-500}`, `{rounded.md}`, etc.) and the spec's closed property set: `backgroundColor`, `textColor`, `typography`, `rounded`, `padding`, `size`, `height`, `width`.

The prose specs are the human-readable explanation; the YAML is the machine-readable contract. They must agree.

- **Buttons**: 4–5 variants (primary brand, secondary/neutral, white/light, dark, outline/ghost). Each gets: background, text color, padding, radius, shadow, and a one-line personality description. State variants (`-hover`, `-active`, `-disabled`) are defined as separate front matter entries.
- **Cards & Containers**: Surface colors, borders, radius scale, shadow treatment for light and dark contexts.
- **Inputs & Forms**: Text color, padding, border, focus state, radius.
- **Navigation**: Layout approach, colors, border, CTA placement, hover behavior.
- **Image Treatment**: Corner radius, shadow, container approach.
- **Distinctive Components** (optional): Any brand-specific patterns worth calling out.

### 8. Do's and Don'ts

_(maps to spec's "Do's and Don'ts" section)_

- **Do** (8–10 items): Specific, actionable rules that reinforce the brand.
- **Don't** (8–10 items): Common mistakes to avoid, referencing specific values or token names.

### 9. Responsive Behavior

_(Golden Hippo extension — preserved by the spec)_

- **Breakpoints table**: Use the standardized Golden Hippo breakpoints from `references/layout-tokens.md` (sm: 640px, md: 768px, lg: 1024px, xl: 1280px, 2xl: 1536px). For each breakpoint, describe the key layout changes specific to this brand.
- **Touch Targets**: Minimum sizes and padding.
- **Collapsing Strategy**: How nav, grids, hero, cards, and spacing adapt at each breakpoint.
- **Image Behavior**: Scaling, aspect ratios, art direction notes.

### 10. Agent Prompt Guide

_(Golden Hippo extension — preserved by the spec)_

This section is specifically for AI coding assistants. Include:

- **Quick Color Reference**: Include the anchor step plus 2–3 most commonly used adjacent steps for each brand color (e.g., hover, pressed, text-on-tint steps), plus the key neutral and surface colors. Typically 15–20 entries. Show both the token name (`primary-500`) and the hex. Mark anchor steps with ★.
- **Token Reference Cheat Sheet**: A short list reminding agents that components use `{colors.primary-600}` references, and that the front matter is the authoritative source for any token-level value.
- **Example Component Prompts**: 4–5 copy-paste-ready prompts that demonstrate how to ask an AI to build components using this design system. Each prompt should be a complete sentence referencing exact tokens (e.g., "use the `button-primary` component spec — `{colors.primary-500}` background, `{colors.neutral-50}` text, `{rounded.md}` corners").
- **Iteration Guide**: 5–7 numbered rules for how to communicate with AI tools about this design system.
