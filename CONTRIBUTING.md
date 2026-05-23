# Contributing to SVG Animation Studio

Thank you for being here. The best contributions to this project right now are **example scenes** — not code. A great example scene teaches other creators what's possible and gives them a real starting point.

You don't need to be a developer. If you can build something in the studio and export the JSON, you can contribute.

---

## What we're looking for

### Example scenes (highest priority)

A good example scene:

- **Shows one clear technique** — a loader, a text reveal, a shape transition, an icon animation
- **Is polished** — timing feels right, nothing clips, easing is intentional
- **Is educational** — someone should be able to load it, see how it's built, and learn something
- **Fits in a single scene** — keep `duration` under 10 seconds; multi-scene projects are fine but each scene should be independently interesting
- **Has a descriptive name** — "Staggered Card Reveal" not "my animation"

**Categories we'd love to see:**

- UI patterns — loaders, progress indicators, toggles, skeleton screens
- Text animation — reveals, typewriter, kinetic typography
- Data visualization — bar charts drawing in, number counters, pie segments
- Motion design — logo intros, icon transitions, scene transitions  
- Nature & abstract — particles, waves, generative patterns
- Onboarding flows — feature callouts, tooltips, step sequences

### Documentation

- Workflow guides ("How to build a staggered reveal from scratch")
- Tips & tricks ("Getting spring easing to feel natural")
- AI prompt recipes ("Prompts that reliably produce good results")
- Corrections and clarifications to existing docs

---

## Submitting an example scene

### Step 1 — Build it

Open [SVG Animation Studio](https://svg-animation-studio.netlify.app) and build your scene. Take your time — polish matters.

**Tips:**
- Use `800 × 500` canvas (the default) so it fits the gallery preview
- Keep the background dark (`#0f172a` or similar) — it looks better in the gallery
- Name your objects clearly in the Layers panel (not "rect-1", "rect-2")
- Test playback a few times before exporting

### Step 2 — Export as JSON

**File → Export → Scene JSON**

Save the file with a descriptive kebab-case name: `card-flip-reveal.json`, `dot-wave-loader.json`.

### Step 3 — Fork and PR

1. Fork this repository
2. Add your JSON file to `examples/`
3. Open a pull request using the [example submission template](.github/PULL_REQUEST_TEMPLATE/example.md)

Fill in the PR template fully — especially the description and what technique it demonstrates.

---

## Example scene JSON schema

Every scene file must be valid JSON conforming to this schema. The app validates on import.

```jsonc
{
  "id": "my-example",          // unique string, kebab-case
  "name": "My Example",        // display name shown in the gallery
  "width": 800,                // canvas width (px) — 800 recommended
  "height": 500,               // canvas height (px) — 500 recommended
  "backgroundColor": "#0f172a",
  "backgroundGradient": { },   // optional — see gradient schema below
  "duration": 2.5,             // total animation duration in seconds
  "objects": [ ],              // array of SceneObject
  "audioTracks": []            // leave empty for examples
}
```

### SceneObject

```jsonc
{
  "id": "obj-1",               // unique within the scene
  "type": "rect",              // rect | circle | ellipse | text | path | star | arrow | triangle
  "name": "Background",        // human-readable label
  "x": 100,                    // left edge (px)
  "y": 100,                    // top edge (px)
  "width": 600,
  "height": 300,
  "fill": "#3b82f6",           // hex color — used as fallback when fillGradient is set
  "fillGradient": { },         // optional — see gradient schema
  "stroke": "none",            // hex color or "none"
  "strokeWidth": 0,
  "opacity": 1,                // 0–1
  "rotation": 0,               // degrees
  "visible": true,
  "locked": false,
  "cssFilter": "",             // optional CSS filter string e.g. "blur(4px)"
  "tracks": [ ]                // array of AnimationTrack
}
```

**Type-specific fields:**
- `circle` / `ellipse`: add `"radius": 40` (half the width)
- `text`: add `"content": "Hello"`, `"fontSize": 24`, `"fontFamily": "system-ui"`
- `path`: add `"d": "M 100 100 C ..."` (SVG path data)
- `star`: add `"points": 5`, `"innerRadius": 20`, `"outerRadius": 40`

### AnimationTrack

```jsonc
{
  "id": "t-1",
  "property": "opacity",       // x | y | rotation | scaleX | scaleY | opacity | fill | motionPath
  "easing": "easeInOut",       // linear | easeIn | easeOut | easeInOut | bounce | spring
                               // or [cx1, cy1, cx2, cy2] for custom cubic-bezier
  "startTime": 0,              // when this track starts (seconds)
  "duration": 1.5,             // how long it runs (seconds)
  "delay": 0,
  "from": 0,                   // start value
  "to": 1,                     // end value
  "loop": "none",              // none | loop | pingpong
  "keyframes": []              // optional array of Keyframe for multi-stop tracks
}
```

### Gradient

```jsonc
{
  "type": "linear",            // linear | radial
  "angle": 0,                  // degrees (linear only)
  "stops": [
    { "offset": 0,   "color": "#6366f1", "opacity": 1 },
    { "offset": 0.5, "color": "#3b82f6", "opacity": 1 },
    { "offset": 1,   "color": "#06b6d4", "opacity": 1 }
  ]
}
```

---

## Quality checklist

Before opening a PR, run through this:

- [ ] Playback looks correct from `t=0` to `t=duration`
- [ ] No object is named "rect-1", "circle-2", etc. — all have descriptive names
- [ ] Canvas is `800 × 500` (or another standard 16:9 / 4:3 size with good reason)
- [ ] JSON is valid (paste into [jsonlint.com](https://jsonlint.com) if unsure)
- [ ] `audioTracks` is `[]`
- [ ] The PR description explains what technique this demonstrates and who it's for

---

## Code of conduct

Be kind. See [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md).