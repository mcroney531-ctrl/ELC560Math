# Unfold — ELC #560

An interactive e-learning lesson built for [E-Learning Heroes Challenge #560](https://community.articulate.com/) — *Teaching Math Online with E-Learning Activities*.

**Live:** <https://mcroney531-ctrl.github.io/ELC560Math/> · or open `index.html` in any browser. No build step, no dependencies — one self-contained file plus one image.

> Spin a solid, unfold it flat into its net, and see exactly where every face, edge and corner lives — then measure it.

## The idea

Most math activities show you a formula and quiz you on it. This one withholds the formula until you've earned it: you meet a shape, fail to count it reliably, unfold it to fix that, then measure a face by hand before anything is calculated for you. **Cal**, a calculator character, guides the whole way from inside the scene.

## Structure

A hub laid out as a **journey map** — a main path of shapes to work through, plus a locked Challenge Zone that opens once the path is clear. Progress is saved to `localStorage`, so stops unlock and step counts persist between visits.

| | Stop | Faces / Edges / Corners | Surface area |
|---|---|---|---|
| 1 | **Unfold the Cube** | 6 / 12 / 8 | 54 cm² |
| 2 | **Unfold the Rectangular Prism** — unlocks when the cube is done | 6 / 12 / 8 | 52 cm² |
| ✦ | **Triangular Prism** — Challenge Zone | 5 / 9 / 6 | 84 cm² |
| ✦ | **Square Pyramid** | 5 / 8 / 5 | 48 cm² |
| ✦ | **Tetrahedron** | 4 / 6 / 4 | 27.7 cm² |
| ✦ | **Octahedron** | 8 / 12 / 6 | 55.4 cm² |

The Challenge Zone opens once both main-path shapes are complete. Its four solids run the same seven-step arc, but they're where the ideas stop being about boxes: faces that aren't all the same shape, triangles measured with ½ × base × height, a solid with a single apex instead of a matching opposite face, and areas that aren't whole numbers.

## The seven-step arc

Each shape runs the same arc, with copy tailored to that solid.

| Step | What happens |
|---|---|
| **Warm-up · Build it up** | The solid sits face-on, looking flat. Drag it and depth appears — a 2D shape becoming 3D. |
| **1 · Look closely** | Spin it and try to count the faces. You run straight into the hidden-faces problem. |
| **2 · The big reveal** | It unfolds flat, each colour-coded face peeling away with a running count. |
| **3 · Meet the net** | Tap all six squares to check them off. Counted faces dim so what's left stands out. |
| **4 · Edges & corners** | One pulsing **Refold** button puts the solid back together. Then you find the parts by tapping the shape itself — one edge, one corner — and each tap lights the whole set and its count, with a nod to Euler's formula. |
| **5 · Why measure?** | Counting gives plain numbers; "how much material covers this?" needs a *unit*. Four real-world cases (gift wrap, paint, cardboard, screen protector). This is where centimetres get earned. |
| **6 · Surface area** | On the cube: drag a ruler onto the glowing face, twice, and watch hashmarks and unit squares appear. Then tap the rest. The prism skips the ruler — scaffold first, release second. |
| **7 · Your turn** | Free play: fold, unfold, spin mid-fold, toggle the overlays. |

## Interactive "Learn more" sheets

Optional depth on five steps — framed as enrichment, never as remediation. Each one adapts to the solid you're working on:

- **From a dot to a solid** — a scrubber that sweeps point → line → square → solid, naming the dimension each stage adds, then ties it to the shape in hand
- **Anatomy explorer** — toggle Faces / Edges / Corners on a wireframe of *this* solid, with the parts round the back shown faded. The wireframe is generated from 3D vertex data, so it's a real octahedron for the octahedron, not a stand-in
- **The net** — for box shapes, a **which of these fold into a cube?** quiz (three work; the 2×3 block doesn't). For the others, their own net drawn out with how it's put together
- **Same face, different unit** — measure one 6 × 6 cm face in 2 cm, 1 cm and ½ cm squares: 9, 36 or 144 of them, always 36 cm²
- **Surface area** — box shapes get three sliders driving `SA = 2(wh + wd + hd)`. The others get a **scale slider**, because that formula doesn't apply to a pyramid: it shows every face's contribution and the fact that doubling the lengths *quadruples* the area

## How it's built

Vanilla HTML/CSS/JS in a single file — no framework, no build, no network calls.

- **The solid** is pure CSS 3D, generated from a net spec. Each face is a polygon in centimetre coordinates plus the edge it hinges on and the solid's dihedral angle there; folding rotates it by `180° − dihedral` about that edge using `rotate(θ) rotateX(φ) rotate(−θ)`, which works for any edge rather than just the axis-aligned ones a box needs. Fold *direction* is derived from which side of the directed hinge the face sits on, so specs never carry hand-tuned signs. Non-rectangles are cut out with `clip-path`, and badges/labels are anchored to the polygon **centroid** — on a right triangle the bounding-box centre can land outside the shape. Rotation is stored as a quaternion to avoid gimbal lock.
- **Correctness is testable**: because every polygon corner gets a vertex dot, folding a net correctly means all dots for a shared vertex land on the same point. Clustering them and comparing the count to the solid's true vertex count verifies a net folds properly — that's how all six shapes are checked.
- **Two worlds**: folded shapes live in a dark "cube mode" space; the flat net switches the stage to a warm blueprint-paper "net mode".
- **One night sky, two strengths**: the journey map sits on a deep violet star field built from layered gradients — no image assets, so the lesson stays one portable file. Inside the activity the same sky is dialled back to a lighter, flatter purple, because the staging area is already a deep cosmic box and a full-strength field around it would swallow the stage. Cards keep light surfaces and dark type throughout; only elements sitting directly on the sky take light ink, and locked cards become frosted glass rather than faded white (dropping a white card's opacity over purple just turns it grey).
- **Guided attention**: a per-face dim overlay lets any moment light exactly one thing. It's an overlay rather than a CSS `filter` because filters cascade into the nested faces.
- **Parts are tappable**: every polygon side carries a hit bar laid along it, so edges can be found on the solid itself. A shared edge ends up with two bars, which costs nothing — tapping either reveals the set. They sit on the face *node* rather than the face, so the clip-path can't trim them, and 3D depth sorting means a bar round the back sits behind an opaque front face and can't be tapped through the solid.
- **Shapes are data** — dimensions, copy and per-step behaviour come from a `SHAPES` config, so adding a solid is mostly a new entry.
