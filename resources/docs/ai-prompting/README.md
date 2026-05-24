# AI prompting guide

The AI assistant has full context of your scene at all times: every object's name, position, size, z-order, all animation tracks, the canvas dimensions, and which object you have selected. Use this.

---

## Two modes

**Edit mode** — Claude generates a JSON patch that modifies your scene. Changes apply immediately with full undo/redo.

**Describe mode** — Claude describes your scene or answers questions about it. Nothing changes. Use this to get feedback on timing, ask why something looks off, or brainstorm.

Switch between modes in the assistant panel header.

---

## What Claude can do

- Add, update, and delete objects and scenes
- Set any object property: position, color, size, opacity, filters, gradients
- Create animation tracks with specific timing, easing, and loop behaviour
- Build multi-object staggered animations in one prompt
- Design complete multi-scene sequences from a brief
- Explain what's already in your scene and suggest improvements

---

## Prompt patterns that work well

### Single-object animation
```
Make the logo scale from 0 to 1 over 0.4 seconds with spring easing, starting at t=0.
```
Be specific: name the object, name the property, give numbers.

### Staggered reveal
```
Fade in all three text objects (Line 1, Line 2, Line 3) from opacity 0 to 1, each
starting 0.2 seconds after the previous one. Use easeOut. Total reveal time: 1.2 seconds.
```
Reference objects by their name from the Layers panel.

### Scene from a brief
```
Build a 3-scene SaaS onboarding animation:
Scene 1 (3s): A headline slides up and fades in, then a subheading follows 0.3s later.
Scene 2 (4s): Three feature icons appear in a staggered cascade, each with a short scale bounce.
Scene 3 (3s): A CTA button pulses twice with a glow, then holds.
Canvas 800×500, dark background (#0f172a), blue accent (#3b82f6).
```

### Surgical edits
```
The "Bar" object's scaleX track — change the easing from easeInOut to linear.
```
```
Slow down all tracks in this scene by 20%.
```
```
Delete the "Glow" object.
```

### Motion design language
```
The card should feel like it has weight — fly in from the bottom, overshoot slightly,
then settle. The overshoot should be subtle, not cartoon-like.
```
Claude interprets "weight", "settle", "overshoot" into appropriate easing choices.

### Describing what you see
Switch to Describe mode:
```
Why does the circle feel jerky between 2s and 3s?
```
```
What would make this transition feel more professional?
```
```
Is there anything in this scene that would cause problems in the SMIL export?
```

---

## Tips

**Name your objects before prompting.** Claude references objects by name. "The logo" is clearer than "the circle in the top-left".

**Give canvas context.** "Place it in the center" means different things at 800×500 vs 400×300. The AI knows your canvas size, but explicit coordinates ("at x=100, y=200") are always unambiguous.

**Iterate.** Start with a rough version ("add a fade-in to the title") then refine ("make the fade slower, start at t=1, use easeOut"). Each step is undoable.

**Use Describe mode for feedback.** Before exporting, ask "Does anything in this scene look unintentional?" or "What would you change about the timing?".

**Multi-step in one prompt.** Claude handles compound instructions well:
```
Add a drop shadow filter to the "Card" object, move it to x=150 y=180,
and add an opacity track: 0→1 over 0.5s starting at t=0.5, easeOut.
```
