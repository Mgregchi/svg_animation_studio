# Animation concepts

Understanding these five concepts lets you build almost anything in SVG Animation Studio.

---

## Tracks

A track animates one property of one object from a start value to an end value over a set duration. Each track has:

- **Property** — what to animate: `x`, `y`, `opacity`, `rotation`, `scaleX`, `scaleY`, `fill`, or `motionPath`
- **From / To** — the start and end values
- **Start time** — when in the scene timeline this track begins (seconds)
- **Duration** — how long the animation takes (seconds)
- **Delay** — an additional offset before the track starts
- **Easing** — the interpolation curve between From and To
- **Loop** — `none`, `loop` (restarts), or `pingpong` (reverses)

An object can have as many tracks as needed. Tracks for different properties run concurrently and compose automatically.

---

## Easing

Easing controls the *feel* of a transition — how quickly it starts, how it arrives.

| Easing | Behaviour | Use when |
|---|---|---|
| `linear` | Constant speed | Mechanical motion, rotations |
| `easeIn` | Starts slow, ends fast | Objects leaving the screen |
| `easeOut` | Starts fast, ends slow | Objects entering the screen |
| `easeInOut` | Slow start, fast middle, slow end | Most UI transitions |
| `bounce` | Overshoots, bounces back | Playful, physical impacts |
| `spring` | Overshoots, oscillates | Organic, elastic interfaces |

You can also define a **custom cubic-bezier** from the curves editor — drag the handles to build exactly the timing curve you want.

---

## Keyframes

For more complex motion within a single track, add keyframes. A keyframe is a time+value pair that the track must hit at a specific moment. The easing between each pair is independently configurable.

**Example: a ball that bounces realistically**
- `t=0`: y = 60 (top), easing `easeIn`
- `t=0.7`: y = 360 (floor), easing `easeOut`
- `t=1.0`: y = 60 (top), easing `easeIn`

Without keyframes, the track interpolates directly from `from` to `to`. With keyframes, the values in between are controlled precisely.

---

## Loop modes

- **none** — plays once from `from` to `to`, then holds the final value
- **loop** — plays from `from` to `to`, then jumps back to `from` and repeats
- **pingpong** — plays from `from` to `to`, then reverses back to `from`, alternating direction

For loaders and continuous effects: `pingpong` or `loop`.  
For one-shot reveals: `none`.

---

## Stagger

When you want multiple objects to animate with offset timing — a cascade, a wave, a sequential reveal — use the **Stagger** tool in the left panel.

Select all objects you want to stagger, click Stagger, choose a track preset and a delay per object (e.g. 0.15s). The tool applies the same track to each object with an increasing `startTime` offset.

**Common stagger patterns:**
- Fade-in reveal: opacity 0→1, 0.15s stagger, easeOut
- Slide-up reveal: opacity 0→1 + y offset 30px→0, 0.15s stagger
- Wave: scaleY 0.4→1.5, 0.18s stagger, pingpong

---

## Motion paths

Instead of animating `x` and `y` separately, a `motionPath` track moves an object along an SVG Bezier path. The object follows the curve smoothly at the specified easing.

Use motion paths for: circular orbits, arc animations, following a layout guide, animated arrows pointing at things.

The path is specified as an SVG `d` attribute string. You can draw it by hand or generate one with the AI assistant ("make the circle follow a figure-8 path across the canvas").
