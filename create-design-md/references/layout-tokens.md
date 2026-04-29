# Standardized Layout Tokens

All Golden Hippo brands use these exact spacing, shadow, and breakpoint values. These are NOT customizable per-brand — they ship as-is in every DESIGN.md.

## Spacing Scale

The full spacing scale, used for padding, margin, gap, and sizing:

0px, 2px, 4px, 6px, 8px, 12px, 16px, 20px, 24px, 32px, 40px, 48px, 64px, 80px, 96px, 128px, 160px, 192px, 224px, 256px, 320px, 384px, 480px, 560px, 640px, 720px, 768px, 1024px, 1280px, 1440px, 1600px, 1920px

**Common usage patterns:**

- Component internal padding: 8px–24px
- Button padding: typically 8px–12px vertical, 16px–24px horizontal
- Card padding: 16px–32px
- Section vertical spacing: 64px–128px
- Stack gaps (small): 4px–8px
- Stack gaps (medium): 12px–24px
- Stack gaps (large): 32px–64px
- Page max-widths: 768px, 1024px, 1280px, 1440px

## Shadow Scale

Named shadow tokens from smallest to largest:

| Name | Value                                                                 | Use                                                  |
| ---- | --------------------------------------------------------------------- | ---------------------------------------------------- |
| 2xs  | `0 1px rgb(0 0 0 / 0.05)`                                             | Barely-there lift — subtle card edges, input borders |
| xs   | `0 1px 2px 0 rgb(0 0 0 / 0.05)`                                       | Light elevation — secondary buttons, resting cards   |
| sm   | `0 1px 3px 0 rgb(0 0 0 / 0.1), 0 1px 2px -1px rgb(0 0 0 / 0.1)`       | Standard elevation — cards, dropdowns at rest        |
| md   | `0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1)`    | Moderate lift — hovered cards, active dropdowns      |
| lg   | `0 10px 15px -3px rgb(0 0 0 / 0.1), 0 4px 6px -4px rgb(0 0 0 / 0.1)`  | High elevation — popovers, floating toolbars         |
| xl   | `0 20px 25px -5px rgb(0 0 0 / 0.1), 0 8px 10px -6px rgb(0 0 0 / 0.1)` | Maximum lift — modals, command palettes              |
| 2xl  | `0 25px 50px -12px rgb(0 0 0 / 0.25)`                                 | Dramatic — hero images, feature showcases            |

## Breakpoints

| Name | Min Width | Typical Use                                                 |
| ---- | --------- | ----------------------------------------------------------- |
| sm   | 640px     | Large phones / small tablets — single column with more room |
| md   | 768px     | Tablets — 2-column layouts begin                            |
| lg   | 1024px    | Small desktops — full nav, 3+ column grids                  |
| xl   | 1280px    | Standard desktops — maximum content width for most layouts  |
| 2xl  | 1536px    | Large desktops — expanded grids, wider containers           |

**Mobile-first**: All styles default to the smallest screen. Breakpoints layer on complexity as width increases.
