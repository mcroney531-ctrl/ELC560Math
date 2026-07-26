# Unfold — ELC #560

An interactive e-learning lesson built for [E-Learning Heroes Challenge #560](https://community.articulate.com/) — *Teaching Math Online with E-Learning Activities*.

**Live:** <https://mcroney531-ctrl.github.io/ELC560Math/> · or open `index.html` in any browser. No build step, no dependencies — one self-contained file plus one image.

> Spin a solid, unfold it flat into its net, and see exactly where every face, edge and corner lives — then measure it.

## The idea

Most math activities show you a formula and quiz you on it. This one withholds the formula until you've earned it: you meet a shape, fail to count it reliably, unfold it to fix that, then measure a face by hand before anything is calculated for you. **Cal**, a calculator character, guides the whole way from inside the scene.

## Structure

A hub laid out as a **journey map** — a main path of shapes to work through, plus a locked Challenge Zone that opens once the path is clear. Progress is saved to `localStorage`, so stops unlock and step counts persist between visits.

| | Stop | State |
|---|---|---|
| 1 | **Unfold the Cube** | Available |
| 2 | **Unfold the Rectangular Prism** | Unlocks when the cube is finished |
| — | Triangular prism, pyramid, tetrahedron, octahedron | Challenge Zone (locked) |

## The seven-step arc

Each shape runs the same arc, with copy tailored to that solid.

| Step | What happens |
|---|---|
| **Warm-up · Build it up** | The solid sits face-on, looking flat. Drag it and depth appears — a 2D shape becoming 3D. |
| **1 · Look closely** | Spin it and try to count the faces. You run straight into the hidden-faces problem. |
| **2 · The big reveal** | It unfolds flat, each colour-coded face peeling away with a running count. |
| **3 · Meet the net** | Tap all six squares to check them off. Counted faces dim so what's left stands out. |
| **4 · Edges & corners** | Fold it back up, light up the 12 edges and 8 corners, with a nod to Euler's formula. |
| **5 · Why measure?** | Counting gives plain numbers; "how much material covers this?" needs a *unit*. Four real-world cases (gift wrap, paint, cardboard, screen protector). This is where centimetres get earned. |
| **6 · Surface area** | On the cube: drag a ruler onto the glowing face, twice, and watch hashmarks and unit squares appear. Then tap the rest. The prism skips the ruler — scaffold first, release second. |
| **7 · Your turn** | Free play: fold, unfold, spin mid-fold, toggle the overlays. |

## Interactive "Learn more" sheets

Optional depth on five steps — framed as enrichment, never as remediation:

- **From a dot to a solid** — a scrubber that sweeps point → line → square → cube, naming the dimension each stage adds
- **Cube anatomy** — toggle Faces / Edges / Corners on a wireframe cube to see 6 / 12 / 8, hidden parts included
- **Which of these fold into a cube?** — tap four arrangements of six squares to test them; three work, the 2×3 block doesn't
- **Same face, different unit** — measure one 6 × 6 cm face in 2 cm, 1 cm and ½ cm squares: 9, 36 or 144 of them, always 36 cm²
- **Build the formula** — three sliders drive a live breakdown of `SA = 2(wh + wd + hd)`

## How it's built

Vanilla HTML/CSS/JS in a single file — no framework, no build, no network calls.

- **The solid** is pure CSS 3D: six faces on a hinged DOM hierarchy driven by one fold-progress value, so the fold animation, the scrub slider and the net layout all share the same geometry. Rotation is stored as a quaternion to avoid gimbal lock.
- **Two worlds**: folded shapes live in a dark "cube mode" space; the flat net switches the stage to a warm blueprint-paper "net mode".
- **Guided attention**: a per-face dim overlay lets any moment light exactly one thing. It's an overlay rather than a CSS `filter` because filters cascade into the nested faces.
- **Shapes are data** — dimensions, copy and per-step behaviour come from a `SHAPES` config, so adding a solid is mostly a new entry.
