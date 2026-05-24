# Scene JSON schema reference

This is the complete technical reference for the SVG Animation Studio scene format. You need this if you're building scenes by hand, writing tooling, or contributing examples.

---

## Top-level scene

```typescript
{
  id: string                    // unique identifier
  name: string                  // display name
  width: number                 // canvas width in px
  height: number                // canvas height in px
  backgroundColor: string       // hex color e.g. "#0f172a"
  backgroundGradient?: GradientDef   // optional gradient overlay
  duration: number              // total scene duration in seconds
  objects: SceneObject[]
  audioTracks: AudioTrack[]     // leave [] for example scenes
}
```

---

## SceneObject

```typescript
{
  id: string
  type: "rect" | "circle" | "ellipse" | "text" | "path" | "star" | "arrow" | "triangle"
  name: string                  // human-readable label shown in Layers panel
  x: number                     // left edge in px
  y: number                     // top edge in px
  width: number
  height: number
  fill: string                  // hex color — solid fallback when fillGradient is set
  fillGradient?: GradientDef
  stroke: string                // hex color or "none"
  strokeWidth: number
  opacity: number               // 0–1
  rotation: number              // degrees
  visible: boolean
  locked: boolean
  cssFilter?: string            // CSS filter string e.g. "blur(4px)" or "drop-shadow(0 0 6px #3b82f6)"
  tracks: AnimationTrack[]
}
```

### Type-specific fields

**circle / ellipse**
```typescript
radius: number    // half the width — must equal width/2
```

**text**
```typescript
content: string
fontSize: number
fontFamily: string    // e.g. "system-ui", "monospace"
```

**path**
```typescript
d: string    // SVG path data attribute
```

**star**
```typescript
points: number       // number of points e.g. 5
innerRadius: number  // inner radius in px
outerRadius: number  // outer radius in px
```

---

## AnimationTrack

```typescript
{
  id: string
  property: "x" | "y" | "rotation" | "scaleX" | "scaleY" | "opacity" | "fill" | "motionPath"
  easing: EasingName | [number, number, number, number]  // named or cubic-bezier
  startTime: number    // seconds from scene start
  duration: number     // seconds
  delay: number        // additional offset in seconds
  from: number | string    // string for "fill" (hex color) and "motionPath" (SVG d)
  to: number | string
  loop: "none" | "loop" | "pingpong"
  keyframes: Keyframe[]
}
```

**EasingName values:** `"linear"` `"easeIn"` `"easeOut"` `"easeInOut"` `"bounce"` `"spring"`

**Custom cubic-bezier:** `[cx1, cy1, cx2, cy2]` — same control points as CSS `cubic-bezier()`.

---

## Keyframe

```typescript
{
  id: string
  time: number      // normalized 0–1 within the track's duration
  value: number | string
  easing: EasingName    // easing from this keyframe to the next
}
```

When keyframes are present, the track's `from`/`to` are overridden by the keyframe values. The first keyframe should have `time: 0`, the last `time: 1`.

---

## GradientDef

```typescript
{
  type: "linear" | "radial"
  angle: number    // degrees, linear only (0 = left→right, 90 = top→bottom)
  stops: GradientStop[]
}

{
  offset: number    // 0–1 position along the gradient
  color: string     // hex color
  opacity: number   // 0–1
}
```

---

## AudioTrack

```typescript
{
  id: string
  name: string
  src: string          // "idb:<key>" for IndexedDB-stored files, or https:// URL
  startTime: number    // when in the scene this track starts playing
  audioOffset: number  // trim: start playing from this offset into the audio file
  duration: number     // how long to play
  volume: number       // 0–1
  loop: boolean
  muted: boolean
}
```

---

## Allowed cssFilter values

For security, the following CSS filter functions are allowed:

`blur()` `brightness()` `contrast()` `drop-shadow()` `grayscale()` `hue-rotate()` `invert()` `opacity()` `saturate()` `sepia()` `none`

`url()` references and arbitrary values are rejected.
