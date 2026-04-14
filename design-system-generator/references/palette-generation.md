# Brand Color Palette Generation

Each user-provided brand color is expanded into a full 11-step perceptually balanced palette (50, 100, 200, 300, 400, 500, 600, 700, 800, 900, 950) using the OKLCH color space. The user's original hex value is preserved exactly at its **anchor step** — the step whose target lightness most closely matches the color's natural lightness.

## Step 1: Determine the Anchor Step

Convert the user's hex color to OKLCH and read its lightness (L). Compare against the target lightness values below to find the closest step:

| Step | Target Lightness |
|------|-----------------|
| 50   | 0.975 |
| 100  | 0.935 |
| 200  | 0.870 |
| 300  | 0.790 |
| 400  | 0.680 |
| 500  | 0.560 |
| 600  | 0.470 |
| 700  | 0.390 |
| 800  | 0.320 |
| 900  | 0.250 |
| 950  | 0.170 |

Pick the step with the smallest absolute difference from the color's actual L value. This is the anchor step — the user's hex sits here unchanged.

### Typical anchor positions by color character

- **Deep/dark saturated colors** (dark blue, burgundy, forest green) → usually anchor at 600 or 700
- **Medium vibrant colors** (true red, bright green, electric lime, saturated orange) → usually anchor at 400 or 500
- **Light/pastel colors** (soft coral, lavender, light pink, peach) → usually anchor at 200 or 300

The anchor step matters because it determines how the palette reads. A bright lime at 200 looks unnaturally vibrant compared to adjacent palettes — move it to 400 where its chroma level belongs, and the 200 step becomes a proper soft tint.

## Step 2: Generate the Palette

Use `pip install coloraide --break-system-packages` and run the following Python script. Replace the hex values and anchor steps with the user's actual colors:

```python
from coloraide import Color

def generate_palette(hex_color, anchor_step):
    base = Color(hex_color).convert("oklch")
    base_L = base['lightness']
    base_C = base['chroma']
    base_H = base['hue']

    target_L = {
        50: 0.975, 100: 0.935, 200: 0.870, 300: 0.790, 400: 0.680,
        500: 0.560, 600: 0.470, 700: 0.390, 800: 0.320, 900: 0.250, 950: 0.170,
    }

    # Chroma profile: peaks at mid-tones, tapers at extremes
    chroma_profile = {
        50: 0.12, 100: 0.25, 200: 0.55, 300: 0.80, 400: 0.95,
        500: 1.0,  600: 0.95, 700: 0.80, 800: 0.60, 900: 0.45, 950: 0.30,
    }

    results = {}
    for step, L in target_L.items():
        if step == anchor_step:
            results[step] = hex_color.upper()
            continue
        new_C = base_C * chroma_profile[step]
        new_color = Color("oklch", [L, new_C, base_H])
        new_color.fit("srgb")  # Gamut map to sRGB
        results[step] = new_color.convert("srgb").to_string(hex=True).upper()

    return results


# ---- Replace these with the user's actual colors ----
colors = [
    ("Primary Name",   "#hexval", anchor_step),
    ("Secondary Name",  "#hexval", anchor_step),
    ("Tertiary Name",  "#hexval", anchor_step),
]

for name, hex_val, anchor in colors:
    palette = generate_palette(hex_val, anchor)
    print(f"\n### {name} (anchor at {anchor})")
    for step in [50, 100, 200, 300, 400, 500, 600, 700, 800, 900, 950]:
        marker = " ★" if step == anchor else ""
        print(f"| {step}{marker} | `{palette[step]}` |")
```

## Step 3: Determine Anchor Steps Programmatically

Before running the palette generation, determine each color's anchor step with this snippet:

```python
from coloraide import Color

target_L = {
    50: 0.975, 100: 0.935, 200: 0.870, 300: 0.790, 400: 0.680,
    500: 0.560, 600: 0.470, 700: 0.390, 800: 0.320, 900: 0.250, 950: 0.170,
}

def find_anchor(hex_color):
    L = Color(hex_color).convert("oklch")['lightness']
    return min(target_L.keys(), key=lambda s: abs(target_L[s] - L))

# Example:
# find_anchor("#2945b8") → 600  (L=0.446, closest to 0.470)
# find_anchor("#c2e329") → 400  (L=0.862, but chroma is extreme — see note below)
# find_anchor("#ffa082") → 300  (L=0.795, closest to 0.790)
```

**Important chroma check:** After finding the anchor, visually sanity-check high-chroma colors. If a color has very high chroma (C > 0.15 in OKLCH) and anchors at a light step (50–200), the palette will look unbalanced — the anchor swatch will visually "pop" far more than the same step in adjacent palettes. In these cases, move the anchor one or two steps darker (e.g., from 200 to 400) so the high-energy color sits at a mid-tone step where that level of saturation is expected. The lighter steps then become proper tints.

## Step 4: Write Usage Guidance

Each palette step needs a one-line usage guidance note. Follow this pattern:

| Step Range | Typical Usage |
|-----------|---------------|
| 50 | Tinted backgrounds, selected state highlights |
| 100 | Badge/tag backgrounds, light card tints, banner fills |
| 200 | Soft tints, hover backgrounds for ghost elements |
| 300 | Focus rings (brand alternative), progress bars, hover states |
| 400 | Mid-tone — secondary links, icon accents, or default for bright colors |
| 500 | Hover state for default buttons (if anchor is 600), text on tinted backgrounds |
| 600 | Default for deep/dark colors — primary buttons, nav, hero backgrounds |
| 700 | Pressed/active states, dark-theme CTAs |
| 800 | Text on light-tinted backgrounds (50–200), dark header bars |
| 900 | Deep dark overlays, footer alternatives |
| 950 | Near-black with color undertone — dark theme backgrounds |

Adapt these to the specific brand context. The anchor step's guidance should be bolded and marked as the **default** usage.

## Integration with Component Specs

When the palette is generated, update component specs to reference palette steps instead of ad-hoc darkened values:

- **Button hover** → one step lighter than anchor (e.g., anchor at 600 → hover is 500)
- **Button pressed/active** → one step darker than anchor (e.g., anchor at 600 → pressed is 700)
- **Text on tinted backgrounds** → use the 800 step of the same palette
- **Light tint backgrounds** → use the 50 step of the same palette

The Agent Prompt Guide's Quick Color Reference should include the anchor step plus the 2–3 most commonly used adjacent steps for each brand color.
