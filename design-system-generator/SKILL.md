---
name: design-system-generator
description: Generate a comprehensive DESIGN.md design system document through a guided interview. Use this skill whenever the user wants to create a design system, brand guide, style guide, or DESIGN.md file. Also trigger when the user mentions "design tokens", "color palette", "brand colors", "design spec", or asks to document their visual identity for a project. Even if the user just says something like "help me set up a design system for my app" or "I need a DESIGN.md" — this is the skill. Covers color palettes (with Tailwind neutral integration), typography, component styles, layout, elevation, responsive behavior, and AI agent prompt guides.
---

# Design System Generator

Generate a detailed, opinionated DESIGN.md file by interviewing the user about their brand. The output follows a standardized 9-section structure covering visual theme, colors, typography, components, layout, elevation, do's/don'ts, responsive behavior, and an AI agent prompt guide.

## Interview Flow

Gather information from the user step by step using the `ask_user_input` tool where possible. Don't dump all questions at once — keep it conversational, one step at a time.

### Step 1: Brand Foundation

Start by asking the user to describe their brand in a few sentences. What's the product? What's the personality? Who's the audience?

This is a plain conversational question — no buttons needed. The answer informs the tone and editorial voice of the entire DESIGN.md.

### Step 2: Brand Colors

Ask the user for their three brand colors as hex codes. Frame it clearly:

> I need three hex codes from you:
>
> - **Primary** — your main brand color, used for CTAs and key brand moments
> - **Secondary** — a supporting color for accents, secondary actions, hover states
> - **Tertiary** — a third color for variety, illustrations, or subtle highlights

If the user provides fewer than three, ask for the remaining ones. If they're unsure about secondary/tertiary, offer to suggest options that complement their primary — but always confirm before proceeding.

### Step 3: Neutral Palette

Use `ask_user_input` with a `single_select` question to let the user pick from Tailwind's five default neutral palettes:

- **Stone** — warm, earthy, sandy undertone (pairs well with warm brand colors)
- **Gray** — balanced, faintly cool, the most versatile
- **Neutral** — true neutral, no undertone, stays invisible behind brand colors
- **Zinc** — cool, metallic, blue-gray undertone (modern/tech feel)
- **Slate** — the coolest, distinctly blue-toned (editorial/polished feel)

Include a brief personality note for each option so the user can make an informed choice. After they select, read the full palette values from `references/tailwind-neutrals.md`.

### Step 4: Typography

Ask the user about their font preferences using `ask_user_input`:

**Question 1** (single_select): What kind of headline font?

- Serif (classic, editorial, authoritative)
- Sans-serif (clean, modern, approachable)
- Monospace (technical, developer-focused)
- I have a specific font in mind

If they pick "I have a specific font in mind" — ask them to name it in a follow-up conversational question.

**Question 2** (single_select): What about body/UI text?

- Same family as headlines
- Sans-serif (if headlines are serif)
- I have a specific font in mind

Again, follow up conversationally if they have a specific font.

**Question 3** (single_select): Code font preference?

- System monospace (Menlo, Consolas, etc.)
- JetBrains Mono
- Fira Code
- I have a specific font in mind

For any custom fonts the user names, determine sensible fallbacks (e.g., a Google Font with `Inter` as fallback, or a serif with `Georgia` as fallback).

### Step 5: Brand Vibe (Optional Refinement)

Use `ask_user_input` with a `multi_select` to let the user tag the overall aesthetic. This helps calibrate the editorial tone and component styling:

- Minimal & clean
- Bold & expressive
- Warm & organic
- Cool & technical
- Playful & energetic
- Luxurious & refined
- Developer-focused
- Editorial & literary

These selections influence the writing tone of Section 1 (Visual Theme & Atmosphere), the shadow/elevation philosophy, border radius defaults, and the Do's/Don'ts.

### Step 6: Confirm & Generate

Summarize back what you've collected:

- Brand description
- Primary / Secondary / Tertiary colors (with hex)
- Neutral palette choice
- Headline / Body / Code fonts
- Vibe tags

Ask the user to confirm or adjust anything before generating.

---

## Generation

Once confirmed, read these reference files before generating:

- `references/output-structure.md` — the 9-section template
- `references/tailwind-neutrals.md` — neutral palette hex values
- `references/semantic-colors.md` — standardized error/info/success/warning palettes
- `references/layout-tokens.md` — standardized spacing, shadows, and breakpoints
- `references/palette-generation.md` — algorithm for expanding brand colors into 11-step palettes

### Brand color palette generation (required)

Before writing the DESIGN.md, generate full 11-step palettes for each of the three brand colors. Read `references/palette-generation.md` for the complete algorithm. The short version:

1. Install `coloraide` (`pip install coloraide --break-system-packages`)
2. For each brand color, convert to OKLCH and find its natural anchor step by comparing its lightness to the target lightness table
3. Sanity-check high-chroma colors — if a color has chroma > 0.15 and anchors at a light step (50–200), move the anchor 1–2 steps darker so the palette reads evenly across all three brand colors
4. Run the palette generation script from the reference file
5. Include the full 11-step palette for each brand color in the DESIGN.md, with the user's original hex preserved exactly at the anchor step (marked with ★)

Each step gets a one-line usage guidance note. The anchor step is bolded as the default. Component specs (buttons, hover states, pressed states) should reference specific palette steps rather than ad-hoc darkened values.

This is NOT optional — every DESIGN.md must include expanded palettes for all three brand colors.

Then generate the DESIGN.md following every section in the output structure reference.

### Standardized tokens (do NOT customize per-brand)

All Golden Hippo brands share the same values for these tokens. Read them from the reference files and include them verbatim in every DESIGN.md:

- **Semantic color palettes**: Error/Danger uses Tailwind Red, Info uses Tailwind Blue, Success uses Tailwind Green, Warning uses Tailwind Yellow. Include the full 50–950 scale for each, with role mappings (text, background, border) for both light and dark surfaces. These are read from `references/semantic-colors.md`.
- **Spacing scale**: The full scale from 0px to 1920px. Read from `references/layout-tokens.md`.
- **Shadow scale**: Named tokens from `2xs` through `2xl`. Read from `references/layout-tokens.md`.
- **Breakpoints**: sm (640px), md (768px), lg (1024px), xl (1280px), 2xl (1536px). Read from `references/layout-tokens.md`.

These standardized values replace any brand-specific derivation for those sections. The brand-specific work is in the primary/secondary/tertiary colors, neutral palette mapping, typography, component styling, editorial tone, and the agent prompt guide.

### Key generation principles

**Be opinionated, not generic.** The example DESIGN.md doesn't just list colors — it describes _why_ they work and what emotional register they hit. Every color gets a descriptive name (not "Primary Blue" but something with personality like "Deep Ocean" or "Ember"). Every section should feel like it was written by a designer with strong opinions about the brand.

**Use the standardized semantic palettes.** Don't derive custom error/success/warning colors. All brands use the same Tailwind-based semantic palettes from `references/semantic-colors.md`. Include them in the "Secondary & Accent" color section with their full step ranges and role mappings.

**Map the neutral palette to real roles.** The selected Tailwind palette provides the full step range (50–950). Map specific steps to concrete surface, text, and border roles. Use the personality notes from the reference to inform how you describe these neutrals.

**Typography hierarchy must be complete.** Include every level from Display (largest) to Micro (smallest), plus Code. Sizes should form a coherent scale. Provide px, rem, weight, line-height, letter-spacing, and contextual notes for each level.

**Component specs should be buildable.** A developer should be able to read a button spec and implement it without guessing. Include exact colors, padding, radius, and shadow values. Use the standardized shadow token names (2xs, xs, sm, md, lg, xl, 2xl) instead of raw CSS values when specifying component shadows.

**The Agent Prompt Guide is critical.** This section makes the design system usable with AI coding tools. The example component prompts should be specific enough to copy-paste into an AI chat and get a correctly styled component back. Shadow references in prompts should use token names (e.g., "shadow-sm") alongside the raw values.

### Output

1. Write the complete DESIGN.md to `/mnt/user-data/outputs/DESIGN.md`
2. Present the file to the user using `present_files`
3. Follow up with a brief summary of the key design decisions — keep it to 3–4 sentences highlighting the most distinctive choices (not a section-by-section recap)
