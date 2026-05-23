# SVG Animation Studio

**Design, animate, and ship vector motion — entirely in your browser.**

No installs. No accounts. No runtime dependencies. Start creating now at [svg-animation-studio.netlify.app](https://svg-animation-studio.netlify.app).

---

## What you can build

A fintech dashboard where numbers count up and charts draw themselves. A logo that breathes. A loading screen that doesn't feel like waiting. An onboarding flow that teaches without words. An icon pack where every element has its own personality.

SVG Animation Studio is built for the moments when a static design isn't enough — when you need motion that's **lightweight enough to ship on the web**, precise enough to feel professional, and fast enough to iterate in real time.

---

## Why SVG, and why it matters

Most web animation tools produce one of three things: a heavy JavaScript bundle, a video file, or a GIF. SVG animation produces none of those. A fully animated SVG scene can weigh **under 10KB** and run at 60fps without a single line of JavaScript. It scales to any screen without pixelating. It's accessible, indexable, and CSS-styleable.

The web has had native animation primitives for years. Most tools just haven't made them approachable. This one does.

---

## Key capabilities

- **Multi-scene projects** — each scene is an independent canvas with its own objects, duration, and audio. String scenes together for sequential playback or manage them as standalone assets.
- **Full animation control** — per-object tracks for position (X, Y, or along a motion path), rotation, scale, opacity, and fill color. Six easing modes: linear, ease-in/out, bounce, spring. Loop, ping-pong, and delay on every track. Keyframe-level timing within any track.
- **Audio synchronisation** — attach audio tracks per scene. Local files stored in IndexedDB (never uploaded anywhere). Precise start time, offset trimming, duration, volume, mute, and loop controls. Audio is baked into the HTML export.
- **Timeline** — zoomable timeline with color-coded track bars and a scrub playhead. Object tracks and audio tracks shown together. Click any bar to jump to that track's editor.
- **Stagger system** — apply any animation preset to every object in a scene with a per-object delay offset. One click to create cascading reveals, wave effects, and choreographed entrances.
- **CSS filter effects** — drop shadows, glows, blur, and custom filter strings per object. Presets included.
- **Export without compromise** — five export formats covering every use case. What you design is exactly what ships.
- **Undo everything** — 100-step history. Every object, track, keyframe, scene, and audio change is undoable.

---

## Export formats

| Format | Best for | Audio Support |
|---|---|---|
| **Static SVG** | Icons, illustrations, open-graph images | — |
| **Animated SVG (SMIL)** | Self-playing web assets; embed in `<img>` or `<object>` | — |
| **Animated SVG (CSS)** | Maximum browser compat; editable via CSS | — |
| **Scene JSON** | Saving, sharing, version control, re-importing | Refs only |
| **HTML + Audio** | Full presentations, demos, standalone playable pages | ✓ Inline |

Exported HTML files are fully self-contained — no CDN, no fonts, no external requests. Drop the file anywhere and it plays.

---

## Community resources (This repository)

This repository serves as the community hub for SVG Animation Studio. Here you will find example scenes, guides, templates, and resources to help you build animations.

### Repository structure

| Folder | Contents |
|---|---|
| [`examples/`](examples/) | Curated example scenes (JSON) — load any into the editor in one click |
| [`.github/`](.github/) | Pull request and issue templates for contributing |

### Current example scenes

| Name | Tags | What it demonstrates |
|---|---|---|
| Bouncing Ball | motion, beginner, loop | Keyframe animation with squash & stretch |
| Logo Reveal | reveal, text, beginner | Staggered two-element entrance |
| Color Pulse | color, loop, beginner | Ping-pong fill color loop |
| Motion Path | motion, path, intermediate | 2D Bezier motion path track |
| Progress Bar | ui, gradient, intermediate | Gradient scaleX animation, opacity crossfade |
| Staggered Text Reveal | text, reveal, intermediate | Multi-object entrance with stagger delay |
| Loading Spinner | ui, loop, beginner | Three-dot scaleY ping-pong with offsets |
| Gradient Sunrise | gradient, motion, intermediate | Radial gradient + y-position animation |

Want to share your own creations? Check out [CONTRIBUTING.md](CONTRIBUTING.md) to submit a new scene.

---

## How to load an example in the app

1. Open [SVG Animation Studio](https://svg-animation-studio.netlify.app).
2. Click **Help → Examples** in the navigation header.
3. Choose any example to load it as a new scene.
4. Press **Space** to play. Select any object and inspect the timeline/tracks to see how it's built.

---

## AI-assisted motion workflow

SVG Animation Studio features an optional, built-in AI assistant that has full context of your scene (objects, tracks, canvas dimensions, selected items). You can describe the motion you want in plain text to animate them instantly.

### Example prompts:

- **Creating scenes from a brief:**
  > "Create a three-scene fintech onboarding animation. Scene 1: a headline slides in. Scene 2: three feature icons appear in sequence. Scene 3: a call-to-action button pulses. Total duration 12 seconds. Blue and white palette."
- **Motion design:**
  > "The card should fly in from the bottom-right corner, overshoot slightly, and settle. Make it feel like it has weight."
- **Staggered reveals:**
  > "Fade in all objects from opacity 0, each starting 0.1 seconds after the previous one. The animation should complete in 1 second total."
- **Surgical edits:**
  > "The logo rotation track — change the easing from linear to spring and slow it down by 30%."

---

## Keyboard shortcuts

| Key | Action |
|---|---|
| `Space` | Play / Pause |
| `Ctrl + Z` | Undo |
| `Ctrl + Y` / `Ctrl + Shift + Z` | Redo |
| `Ctrl + D` | Duplicate selected object |
| `Ctrl + C` / `Ctrl + V` | Copy / Paste object |
| `Delete` / `Backspace` | Delete selected object |
| `]` / `[` | Move object forward / backward one layer |
| `Shift + ]` / `Shift + [` | Move object to front / back |
| `← ↑ → ↓` | Nudge 1 px |
| `Shift + ← ↑ → ↓` | Nudge 10 px |
| `Ctrl + scroll` | Zoom canvas (15%–400%) |
| `Alt + drag` | Pan canvas |
| `Escape` | Deselect |

---

## Technical stack

Under the hood, the application is built on modern web standards with zero UI frameworks:

- **Core:** React 18 + Vite + TypeScript (strict mode)
- **State Management:** Zustand for state, Zundo for undo/redo history
- **Animation Engine:** Custom RAF (requestAnimationFrame) loop — no heavy external animation libraries
- **Audio Integration:** Web Audio API with local IndexedDB storage (assets stay in your browser)
- **Hosting / AI Proxy:** Cloudflare Worker proxying for secure AI assistant communication

---

MIT License · Built by [@Mgregchi](https://github.com/Mgregchi)