# Getting started

SVG Animation Studio runs entirely in your browser. Nothing to install for the basic experience.

---

## Option A — Use the hosted app

Open the app, click **Launch Studio**, and start building. The AI assistant requires a Cloudflare Worker proxy (see [ai-setup.md](ai-setup.md)).

## Option B — Run locally

```bash
git clone https://github.com/Mgregchi/svg-animation-studio.git
cd svg-animation-studio
npm install
npm run dev
```

Open `http://localhost:5173`.

---

## Your first animation in 5 minutes

### 1. Add a shape
Click any shape in the left panel (Rect, Circle, Text, etc.). It lands in the center of the canvas.

### 2. Select and position it
Click the object on the canvas. Drag it, or type exact X/Y values in the Properties panel on the right. Resize using the 8 handles around it.

### 3. Add an animation track
With the object selected, open the **Animation** section in the right panel. Click **Add Track**, choose a property (try `opacity` or `y`), set **From**, **To**, and **Duration**. Click outside the panel.

### 4. Play it
Press **Space**. The object animates. Press **Space** again to pause.

### 5. Export it
Open the Export menu (top toolbar). Choose **Animated SVG (SMIL)** for a self-playing file you can embed anywhere.

---

## Key concepts

**Scene** — one canvas with its own objects, duration, and audio. A project can have multiple scenes.

**Object** — any shape, text, or path on the canvas. Each object has properties (position, color, opacity) and can have multiple animation tracks.

**Track** — animates one property of one object over time. An object can have as many tracks as you like running simultaneously.

**Timeline** — the panel at the bottom shows all tracks as colored bars. Drag the playhead to scrub through time.

**Easing** — how a value changes between its start and end. `easeInOut` is smooth. `bounce` overshoots at the end. `spring` overshoots and oscillates. `linear` is mechanical.
