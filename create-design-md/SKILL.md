---
name: create-design-md
description: Create a comprehensive DESIGN.md design system document through a guided interview. Use this skill whenever the user wants to create a DESIGN.md, design system, brand guide, or style guide. Also trigger when the user mentions "design tokens", "color palette", "brand colors", "design spec", or asks to document their visual identity for a project. Even if the user just says something like "help me set up a design system for my app" or "I need a DESIGN.md" — this is the skill. Output conforms to the Google DESIGN.md spec (YAML front matter + canonical section order) and is lint-validated. Covers color palettes (with Tailwind neutral integration), typography, component styles, layout, elevation, shapes, responsive behavior, and an AI agent prompt guide.
---

# Create DESIGN.md

Generate a detailed, opinionated DESIGN.md file by interviewing the user about their brand. The output conforms to the [Google DESIGN.md spec](https://github.com/google-labs-code/design.md): it begins with a YAML front matter token block, follows the spec's canonical section order, uses the spec's component property names, and is validated with `@google/design.md` after generation.

The 10-section prose structure covers: Visual Theme, Colors, Typography, Layout, Elevation, Shapes, Components, Do's/Don'ts, Responsive Behavior, and an AI Agent Prompt Guide.

## Interview Flow

Gather information from the user step by step using the `ask_user_input` tool where possible. Don't dump all questions at once — keep it conversational, one step at a time.

### Step 1: Brand Foundation

Start by asking the user to describe their brand in a few sentences. What's the product? What's the personality? Who's the audience?

This is a plain conversational question — no buttons needed. The answer informs the tone and editorial voice of the entire DESIGN.md, and becomes the `description` field in the YAML front matter.

### Step 2: Brand Colors

Ask the user for their three brand colors as hex codes. Frame it clearly:

> I need three hex codes from you:
>
> - **Primary** — your main brand color, used for CTAs and key brand moments
> - **Secondary** — a supporting color for accents, secondary actions, hover states
> - **Tertiary** — a third color for variety, illustrations, or subtle highlights

If the user provides fewer than three, ask for the remaining ones. If they're unsure about secondary/tertiary, offer to suggest options that complement their primary — but always confirm before proceeding.

### Step 3: Neutral Palette

Tailwind ships nine default neutral palettes. The picker tool caps at 4 options, so this is a two-step decision: first pick the _family_ (classic vs tinted), then pick the specific palette within it.

**Step 3a** — `ask_user_input` with `single_select`:

> Two families of neutrals to choose from:
>
> - **Classic** — Stone, Gray, Neutral, Zinc, Slate (the traditional five; lower commitment, broader compatibility)
> - **Tinted (v4.2)** — Taupe, Mauve, Mist, Olive (subtle hue lean; more character, more opinion)

Use the personality notes below to give the user context for the two families. If the brand description from Step 1 strongly suggests one family (e.g., a wellness brand → tinted/Mist; a developer tool → classic/Zinc), surface a recommendation but let the user override.

**Step 3b** — `ask_user_input` with `single_select`, options depend on Step 3a:

If the user picked **Classic**, offer:

- **Stone** — warm, earthy, sandy undertone (pairs well with warm brand colors)
- **Gray** — balanced, faintly cool, the most versatile
- **Neutral** — true neutral, no undertone, stays invisible behind brand colors
- **Zinc** — cool, metallic, blue-gray undertone (modern/tech feel)

If the user picked **Classic** and none of the above feels right after seeing the options, follow up conversationally and offer **Slate** as a fifth option (the coolest, distinctly blue-toned, editorial feel). The 4-option cap means Slate gets surfaced via follow-up rather than fitting in the initial picker.

If the user picked **Tinted**, offer:

- **Taupe** — warm red-brown lean (refined, gallery/fashion energy; softer than Stone)
- **Mauve** — subtle violet-pink lean (moody, editorial; pairs with purples)
- **Mist** — cool blue-cyan lean (calm, atmospheric; healthcare/wellness/productivity)
- **Olive** — yellow-green lean (earthy, natural; muted-green brands)

After the user selects, read the full palette values from `references/tailwind-neutrals.md`.

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

Use `ask_user_input` with a `multi_select` to let the user tag the overall aesthetic. This helps calibrate the editorial tone, component styling, and the brand-specific `rounded` scale:

- Minimal & clean
- Bold & expressive
- Warm & organic
- Cool & technical
- Playful & energetic
- Luxurious & refined
- Developer-focused
- Editorial & literary

These selections influence the writing tone of Section 1 (Visual Theme), the shadow/elevation philosophy, the radius values in the Shapes section, and the Do's/Don'ts.

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

- `references/output-structure.md` — the 10-section prose template, in canonical spec order
- `references/yaml-frontmatter.md` — the YAML front matter token schema and component property rules
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
5. Include the full 11-step palette for each brand color in BOTH the YAML front matter (as `primary-50` through `primary-950` token names) AND the prose tables, with the user's original hex preserved exactly at the anchor step (marked with ★ in prose)

Each step gets a one-line usage guidance note in the prose. The anchor step is bolded as the default. Component specs (buttons, hover states, pressed states) reference specific palette steps via `{colors.primary-600}` token references — never raw hex codes.

This is NOT optional — every DESIGN.md must include expanded palettes for all three brand colors.

### YAML front matter generation (required)

The DESIGN.md MUST begin with a YAML front matter block conforming to the [Google DESIGN.md spec](https://github.com/google-labs-code/design.md). See `references/yaml-frontmatter.md` for the complete schema. Key rules:

- The block is the very first thing in the file, fenced by `---`.
- Emit `version: alpha`, `name`, and `description`.
- Emit the full 11-step palette for each brand color (`primary-50` through `primary-950`, etc.), the selected neutral palette steps, and the four standardized semantic palettes (error, info, success, warning).
- Emit semantic aliases (`primary`, `surface`, `on-surface`, etc.) as token references pointing to the appropriate numbered steps.
- Emit every typography level the prose hierarchy defines, using the spec's typography object schema (`fontFamily`, `fontSize`, `fontWeight`, `lineHeight`, `letterSpacing`).
- Emit the brand-specific `rounded` scale.
- Emit the standardized `spacing` scale.
- Emit every component the prose specs, using `{path.to.token}` references and ONLY the spec's closed property set: `backgroundColor`, `textColor`, `typography`, `rounded`, `padding`, `size`, `height`, `width`. State variants (`-hover`, `-active`, `-disabled`) are separate component entries.

The prose sections must agree with the YAML — they're the human-readable explanation, the YAML is the machine-readable contract.

### Section order (required)

Sections appear in this canonical order to pass the spec's `section-order` lint rule:

1. Visual Theme & Atmosphere (spec: Overview)
2. Color Palette & Roles (spec: Colors)
3. Typography Rules (spec: Typography)
4. Layout Principles (spec: Layout)
5. Depth & Elevation (spec: Elevation & Depth)
6. Shapes (spec: Shapes — NEW, split out from Layout)
7. Component Stylings (spec: Components)
8. Do's and Don'ts
9. Responsive Behavior (Golden Hippo extension)
10. Agent Prompt Guide (Golden Hippo extension)

Border radius lives in Shapes, NOT in Layout. This is the most common slip-up — keep Layout focused on spacing and grid.

### Standardized tokens (do NOT customize per-brand)

All Golden Hippo brands share the same values for these tokens. Read them from the reference files and include them verbatim in every DESIGN.md (in both the YAML and the prose):

- **Semantic color palettes**: Error/Danger uses Tailwind Red, Info uses Tailwind Blue, Success uses Tailwind Green, Warning uses Tailwind Yellow. Include the full 50–950 scale for each, with role mappings (text, background, border) for both light and dark surfaces. From `references/semantic-colors.md`.
- **Spacing scale**: The full scale from 0px to 1920px. From `references/layout-tokens.md`.
- **Shadow scale**: Named tokens from `2xs` through `2xl`. From `references/layout-tokens.md`. (Shadows are documented in the Depth & Elevation prose section; they're not part of the spec's YAML schema, so they don't appear in front matter — that's fine, the spec preserves them.)
- **Breakpoints**: sm (640px), md (768px), lg (1024px), xl (1280px), 2xl (1536px). From `references/layout-tokens.md`. (Same — documented in Responsive Behavior prose, not in front matter.)

These standardized values replace any brand-specific derivation for those sections. The brand-specific work is in the primary/secondary/tertiary colors, neutral palette mapping, typography, the `rounded` scale, component styling, editorial tone, and the agent prompt guide.

### Key generation principles

**Be opinionated, not generic.** Every color gets a descriptive name with personality (not "Primary Blue" but something like "Deep Ocean" or "Ember"). Every section should feel like it was written by a designer with strong opinions about the brand.

**Use the standardized semantic palettes.** Don't derive custom error/success/warning colors. All brands use the same Tailwind-based semantic palettes from `references/semantic-colors.md`. Include them in the "Secondary & Accent" color section with their full step ranges and role mappings.

**Map the neutral palette to real roles.** The selected Tailwind palette provides the full step range (50–950). Map specific steps to concrete surface, text, and border roles. Use the personality notes from the reference to inform how you describe these neutrals.

**Typography hierarchy must be complete.** Include every level from Display (largest) to Micro (smallest), plus Code. Sizes form a coherent scale. Provide token name, font, px, rem, weight, line-height, letter-spacing, and contextual notes for each level. Every level in the prose table must have a matching entry in the YAML `typography` map.

**Component specs reference tokens, not hex.** A button-primary spec uses `{colors.primary-500}` for background and `{colors.neutral-50}` for text — never a raw hex code. This is what makes the Tailwind export round-trip cleanly. The prose spec can show the resolved hex in parentheses for readability, but the YAML uses references.

**Use the spec's closed component property set.** `backgroundColor`, `textColor`, `typography`, `rounded`, `padding`, `size`, `height`, `width`. If you find yourself reaching for `borderColor` or `boxShadow`, that's accepted with a warning — but only do it if the property is genuinely needed.

**The Agent Prompt Guide is critical.** This section makes the design system usable with AI coding tools. Example component prompts should reference token names (`{colors.primary-500}`, `{rounded.md}`) so an agent can resolve them against the front matter.

### Output and validation

1. Write the complete DESIGN.md to `/home/claude/DESIGN.md` first (working dir).
2. Run the spec linter against it:
   ```bash
   npx -y @google/design.md lint /home/claude/DESIGN.md
   ```
   The expected outcome:
   - **0 errors** (no broken token references, no duplicate sections)
   - **0 `contrast-ratio` warnings** (every component's `backgroundColor`/`textColor` pair clears WCAG AA at 4.5:1)
   - **0 `section-order` warnings** (sections in canonical order)
   - Some `info` findings are normal (`token-summary` always fires).
3. If lint surfaces errors or warnings other than `info` findings, fix them and re-lint:
   - **broken-ref**: a `{colors.primary-500}` reference points to a token that wasn't emitted — add the missing token or correct the reference.
   - **contrast-ratio**: bump the component's text or background reference to a darker/lighter step (e.g., `{colors.primary-500}` → `{colors.primary-600}`) until the pair clears AA. Re-lint.
   - **section-order**: re-order the H2 sections to match the canonical sequence.
   - **missing-primary** / **missing-typography**: add the missing root tokens.
4. Once lint is clean, copy the file to `/mnt/user-data/outputs/DESIGN.md`.
5. Present the file to the user using `present_files`.
6. Follow up with a brief summary of the key design decisions — keep it to 3–4 sentences highlighting the most distinctive choices (not a section-by-section recap). Mention the lint result (e.g., "Linted clean against the DESIGN.md spec — 0 errors, 0 warnings.").

If the lint command fails to run (network restriction, npx unavailable, etc.), note that in the summary instead of skipping silently — the user should know whether validation actually happened.
