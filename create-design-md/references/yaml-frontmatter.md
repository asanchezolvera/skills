# YAML Front Matter Reference

Every generated DESIGN.md begins with a YAML front matter block conforming to the [Google DESIGN.md spec](https://github.com/google-labs-code/design.md). This block makes the file machine-readable: it can be linted with `npx @google/design.md lint`, diffed against prior versions, and exported to Tailwind theme configs or DTCG `tokens.json`.

The prose sections below the front matter are still the source of editorial intent. The YAML block is just the structured token layer.

## Block placement

The block is the very first thing in the file, fenced by `---` lines:

```
---
version: alpha
name: <Brand Name>
description: <One-line brand description>
colors:
  ...
typography:
  ...
rounded:
  ...
spacing:
  ...
components:
  ...
---

# <Brand Name>

## Visual Theme & Atmosphere
...
```

No content above the opening `---`. No blank line between `---` and `version:`.

## Required fields

- `version: alpha` — pin to the current spec version
- `name: <string>` — the brand name (matches the H1 below)
- `description: <string>` — a single sentence describing the brand

## Colors

Emit the **full 11-step palette** for each brand color, plus the neutral palette steps that get used in the prose, plus the standardized semantic palettes. Names use kebab-case with the step number suffixed.

```yaml
colors:
  # Brand — Primary (descriptive name: "Ember")
  primary-50:  "#fff5f0"
  primary-100: "#ffe4d6"
  primary-200: "#ffc4a8"
  primary-300: "#ff9d75"
  primary-400: "#ff7448"
  primary-500: "#e85423"   # ★ anchor (user's original hex)
  primary-600: "#c43d12"
  primary-700: "#9b2c0b"
  primary-800: "#741f08"
  primary-900: "#4d1305"
  primary-950: "#2b0a02"

  # Brand — Secondary, Tertiary follow the same pattern
  secondary-50: ...
  tertiary-50: ...

  # Neutral (mapped from selected Tailwind palette)
  neutral-50:  "#fafaf9"
  neutral-100: "#f5f5f4"
  ...
  neutral-950: "#0c0a09"

  # Semantic (standardized, from references/semantic-colors.md)
  error-50: "#fef2f2"
  ...
  error-950: "#450a0a"
  info-50: ...
  success-50: ...
  warning-50: ...

  # Convenience aliases — these are what components reference
  primary: "{colors.primary-500}"      # the anchor
  secondary: "{colors.secondary-500}"
  tertiary: "{colors.tertiary-500}"
  surface: "{colors.neutral-50}"
  on-surface: "{colors.neutral-900}"
  border: "{colors.neutral-200}"
  focus-ring: "#3b82f6"
```

The `primary` / `secondary` / `tertiary` / `surface` / `on-surface` aliases are the names the spec recommends. Keeping both the numbered scale AND the semantic aliases means component definitions can use clean references like `{colors.primary}` while exports still get the full palette.

## Typography

Match the prose hierarchy. Names use kebab-case. Emit every level the prose defines:

```yaml
typography:
  display:
    fontFamily: <Headline Font>
    fontSize: 4.5rem
    fontWeight: 700
    lineHeight: 1.05
    letterSpacing: -0.03em
  headline-lg:
    fontFamily: <Headline Font>
    fontSize: 3rem
    fontWeight: 600
    lineHeight: 1.1
    letterSpacing: -0.02em
  headline-md:
    fontFamily: <Headline Font>
    fontSize: 2.25rem
    fontWeight: 600
    lineHeight: 1.15
  headline-sm:
    fontFamily: <Headline Font>
    fontSize: 1.5rem
    fontWeight: 600
    lineHeight: 1.2
  body-lg:
    fontFamily: <Body Font>
    fontSize: 1.125rem
    fontWeight: 400
    lineHeight: 1.6
  body-md:
    fontFamily: <Body Font>
    fontSize: 1rem
    fontWeight: 400
    lineHeight: 1.6
  body-sm:
    fontFamily: <Body Font>
    fontSize: 0.875rem
    fontWeight: 400
    lineHeight: 1.5
  label-md:
    fontFamily: <Body Font>
    fontSize: 0.875rem
    fontWeight: 500
    lineHeight: 1.4
  label-sm:
    fontFamily: <Body Font>
    fontSize: 0.75rem
    fontWeight: 500
    lineHeight: 1.4
    letterSpacing: 0.05em
  caption:
    fontFamily: <Body Font>
    fontSize: 0.75rem
    fontWeight: 400
    lineHeight: 1.4
  code:
    fontFamily: <Code Font>
    fontSize: 0.875rem
    fontWeight: 400
    lineHeight: 1.5
```

Sizes use `rem` (the spec accepts `px`, `em`, `rem` — `rem` is preferred for accessibility). Line height is unitless where possible (multiplier of font-size). Omit fields that don't apply (e.g., no `letterSpacing` if it's the default).

## Rounded

The `rounded` tokens live in the **Shapes** section's prose, but the YAML block holds them at the top level:

```yaml
rounded:
  none: 0px
  sm: 4px
  md: 8px
  lg: 12px
  xl: 16px
  full: 9999px
```

Adjust the values to fit the brand vibe — sharp/architectural brands stay at 0–4px, soft/friendly brands push 8–16px+. The names follow the spec's recommended convention.

## Spacing

Standardized across all Golden Hippo brands. Emit the full scale from `references/layout-tokens.md`:

```yaml
spacing:
  "0": 0px
  "1": 2px
  "2": 4px
  "3": 6px
  "4": 8px
  "5": 12px
  "6": 16px
  "7": 20px
  "8": 24px
  "9": 32px
  "10": 40px
  "11": 48px
  "12": 64px
  "13": 80px
  "14": 96px
  "15": 128px
  # ... continue through the full scale
```

Numeric-only keys must be quoted in YAML. Alternatively, name them semantically (`xs`, `sm`, `md`, `lg`, `xl`) — but the standardized scale has 30+ steps, so numeric indexing is cleaner.

## Components

This is where token references earn their keep. Component definitions use `{path.to.token}` references instead of raw hex codes — this lets the Tailwind exporter wire up theme classes correctly.

Use **only** these property names (the spec's closed set):
`backgroundColor`, `textColor`, `typography`, `rounded`, `padding`, `size`, `height`, `width`.

```yaml
components:
  button-primary:
    backgroundColor: "{colors.primary-500}"
    textColor: "{colors.neutral-50}"
    typography: "{typography.label-md}"
    rounded: "{rounded.md}"
    padding: 12px 24px
    height: 44px
  button-primary-hover:
    backgroundColor: "{colors.primary-600}"
  button-primary-active:
    backgroundColor: "{colors.primary-700}"

  button-secondary:
    backgroundColor: "{colors.neutral-100}"
    textColor: "{colors.neutral-900}"
    typography: "{typography.label-md}"
    rounded: "{rounded.md}"
    padding: 12px 24px
  button-secondary-hover:
    backgroundColor: "{colors.neutral-200}"

  button-outline:
    backgroundColor: transparent
    textColor: "{colors.primary-600}"
    rounded: "{rounded.md}"
    padding: 12px 24px

  card:
    backgroundColor: "{colors.surface}"
    rounded: "{rounded.lg}"
    padding: 24px

  input:
    backgroundColor: "{colors.neutral-50}"
    textColor: "{colors.neutral-900}"
    rounded: "{rounded.md}"
    padding: 12px 16px
    height: 44px
  input-focus:
    backgroundColor: "{colors.neutral-50}"
```

State variants (`-hover`, `-active`, `-focus`, `-disabled`) live as separate component entries with the related key naming convention. The linter recognizes this pattern.

If a component needs a property outside the closed set (e.g., `borderColor`, `boxShadow`), include it — the spec accepts unknown component properties with a warning. Don't reach for that escape hatch unless the property is genuinely needed; the warning is there for a reason.

## Linting expectations

After generation, the lint pass should produce:

- **0 errors**: no broken token references, no duplicate sections
- **0 contrast-ratio warnings**: every component's `backgroundColor`/`textColor` pair clears WCAG AA (4.5:1)
- **0 section-order warnings**: sections appear in the canonical order
- Some `info` findings are expected (token-summary always fires; missing-sections may fire if a brand legitimately omits a section)

If contrast-ratio warnings appear, adjust the component's text or background reference (e.g., bump from `{colors.primary-500}` to `{colors.primary-600}` for a darker background) until the pair clears AA.

## Why aliases AND numbered steps

The spec's example uses flat names (`primary`, `secondary`). Material-style scales use numbered steps (`primary-500`). Emitting both is intentional:

- **Numbered steps** preserve the full palette for Tailwind export and for designers who want to reach for `primary-300` directly.
- **Aliases** (`primary: "{colors.primary-500}"`) make component definitions readable and make the file feel native to the spec's examples.

The DTCG export will deduplicate aliases automatically.
