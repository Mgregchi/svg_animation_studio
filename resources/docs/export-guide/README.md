# Export guide

SVG Animation Studio produces six export formats. Here's when to use each.

---

## Static SVG

**What it is:** A snapshot of your scene at `t=0`. No animation, no JavaScript.

**File size:** Smallest possible — just the vector geometry.

**Use when:**
- Exporting icons, illustrations, or open-graph images
- You need a still frame for documentation or design handoff
- The target environment doesn't support animation

**Limitations:** No animation. If your scene has objects starting at `opacity: 0`, they'll be invisible in the static export.

---

## Animated SVG — SMIL

**What it is:** A fully self-contained SVG file with `<animate>`, `<animateTransform>`, and `<set>` tags baked in. Plays automatically in any browser without JavaScript.

**File size:** Small — a few KB for typical scenes.

**Use when:**
- Embedding in `<img>` tags (plays without JS in the page)
- Email clients that support SVG
- Any context where JavaScript is unavailable or undesirable
- You want the animation to play on load, every time, without user interaction

**Limitations:**
- Fill color gradients animate as solid color (first stop used)
- CSS `filter` effects are preserved but may not animate
- No audio

**Embed in HTML:**
```html
<img src="animation.svg" width="800" height="500" alt="Animation" />
<!-- or for full SVG feature support: -->
<object type="image/svg+xml" data="animation.svg"></object>
```

---

## Animated SVG — CSS

**What it is:** An SVG file where animations are driven by embedded CSS `@keyframes` rather than SMIL attributes.

**File size:** Similar to SMIL, slightly larger due to the style block.

**Use when:**
- You need to override or extend the animation via external CSS
- The target environment has known SMIL support issues
- You want the animation to be easily editable post-export (CSS is more human-readable than SMIL)

**Limitations:** Same as SMIL — no audio, gradient animation limited.

---

## Scene JSON

**What it is:** The raw scene data in the studio's internal format. Not a visual file — it's a save file.

**Use when:**
- Saving your work to disk (supplement to auto-save)
- Sharing a scene with another Studio user
- Version-controlling your animations in Git
- Contributing an example scene to this repo

**Re-importing:** File → Import Scene JSON, or drag the file onto the canvas.

---

## HTML with audio

**What it is:** A fully self-contained HTML file with your animation, a playback controller (play/pause, scrub), and any audio tracks baked in as base64 data URIs.

**File size:** Can be large if audio is included — audio inflates significantly as base64.

**Use when:**
- Delivering a standalone presentation or demo
- Sharing with a client who needs to click play/pause
- The animation has a soundtrack that's essential to the experience
- You want one file that plays anywhere without a server

**Limitations:** Not embeddable in `<img>`. Requires opening in a browser tab.

---

## PNG slide deck (ZIP)

**What it is:** One PNG per scene, rasterized at the scene's native resolution, bundled in a ZIP archive.

**Use when:**
- Using the scenes as presentation slides in Keynote, PowerPoint, or Google Slides
- Handing off static visuals to a non-technical stakeholder
- Generating thumbnails or preview images for each scene

**Limitations:** Static only — no animation, no audio. Scenes that fail to rasterize are skipped with a console warning.

---

## Video (WebM)

**What it is:** A screen-capture recording of your browser tab while the animation plays. Downloads as `.webm`.

**Use when:**
- Posting to social media or a video platform
- Creating a demo video for a product or portfolio
- The target can't play SVG (some mobile contexts, Slack, Notion previews)

**How it works:** Click Record Video, choose this browser tab in the system picker, play your animation, click Stop. The recording is processed and downloaded automatically.

**Limitations:** Quality depends on your display resolution and browser. The user must manually pick the correct tab in the system dialog. WebM plays in Chrome and Firefox natively; macOS users can convert to MP4 with QuickTime.
