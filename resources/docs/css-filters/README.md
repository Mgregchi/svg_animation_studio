# CSS filter effects

Every object supports a CSS filter string applied at render and export time. Filters are great for glow effects, blur backgrounds, and drop shadows that would be tedious to fake with layered shapes.

---

## Using the filter panel

In the Properties panel, scroll to **Filter**. Choose a preset from the dropdown or select **Custom** and type your own filter string.

---

## Available presets

| Preset | Value | Effect |
|---|---|---|
| None | — | No filter |
| Drop Shadow | `drop-shadow(0 4px 6px rgba(0,0,0,0.4))` | Soft shadow below the object |
| Glow (blue) | `drop-shadow(0 0 8px #3b82f6)` | Blue outer glow |
| Glow (white) | `drop-shadow(0 0 8px #ffffff)` | White outer glow |
| Blur | `blur(4px)` | Gaussian blur |
| Blur (subtle) | `blur(2px)` | Light blur for depth of field |

---

## Custom filter strings

You can combine multiple functions in a single filter string:

```css
blur(2px) brightness(1.2)
drop-shadow(0 0 12px #f97316) saturate(1.5)
```

**Allowed functions:** `blur()` `brightness()` `contrast()` `drop-shadow()` `grayscale()` `hue-rotate()` `invert()` `opacity()` `saturate()` `sepia()` `none`

Values outside this set are blocked for security.

---

## Common techniques

### Soft glow behind an object
Place a duplicate of the object beneath it, apply `blur(12px)` and reduce `opacity` to 0.4–0.6. This is more controllable than `drop-shadow` for large glows.

### Frosted glass background
Apply `blur(8px)` to a semi-transparent rect overlaid on content.

### Highlight pulse
Animate the `opacity` of a glow-filtered duplicate from 0 to 0.8 in a `pingpong` loop. Faster than building a proper light rig.

### Scene depth
Apply `blur(2px)` to objects meant to be "in the background". The slight softness reads as distance without any perspective transform.

---

## Export behaviour

- **Static SVG, Animated SVG, HTML:** filters are output as `style="filter: ..."` on the SVG element — they render natively.
- **PNG slides:** filters are rasterized into the PNG (applied via canvas rendering).
- **Video:** filters appear in the recording as-is.

`blur()` and complex `drop-shadow()` filters can be GPU-intensive — test playback performance on target devices if using many simultaneously.
