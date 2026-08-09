# Unfold — ELC #560

An interactive e-learning lesson built for [E-Learning Heroes Challenge #560](https://community.articulate.com/) — *Teaching Math Online with E-Learning Activities*.

**Live:** <https://mcroney531-ctrl.github.io/ELC560Math/> · or open `index.html` in any browser. No build step, no dependencies — one self-contained file plus one image.

> Spin a solid, unfold it flat into its net, and see exactly where every face, edge and corner lives — then measure it.

## The idea

Most math activities show you a formula and quiz you on it. This one withholds the formula until you've earned it: you meet a shape, fail to count it reliably, unfold it to fix that, then measure a face by hand before anything is calculated for you. **Cal**, a calculator character, guides the whole way from inside the scene.

## Structure

The hub has five sections behind a tab bar — **Home**, **Shapes**, **Learn**, **Progress** and **Badges**. Every one is built from data the lesson already keeps (the `SHAPES` config, the five Learn-more sheets, and the saved progress store), so no section can drift out of step with the lesson it describes. Badges in particular are *derived* at render time rather than stored, so they can't disagree with progress.

Home is a **journey map** — a main path of shapes to work through, plus a locked Challenge Zone that opens once the path is clear. Progress is saved to `localStorage`, so stops unlock and step counts persist between visits.

| | Stop | Faces / Edges / Corners | Surface area |
|---|---|---|---|
| 1 | **Unfold the Cube** | 6 / 12 / 8 | 54 cm² |
| 2 | **Unfold the Rectangular Prism** — unlocks when the cube is done | 6 / 12 / 8 | 52 cm² |
| ▲ | **Triangular Prism** — Challenge Zone | 5 / 9 / 6 | 84 cm² |
| ▲ | **Square Pyramid** | 5 / 8 / 5 | 48 cm² |
| ▲ | **Tetrahedron** | 4 / 6 / 4 | 27.7 cm² |
| ▲ | **Octahedron** | 8 / 12 / 6 | 55.4 cm² |

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
| **6 · Surface area** | Every shape measures one face by hand first. Drag the ruler onto the glowing face twice and watch hashmarks and unit squares appear, then tap the rest. On a **rectangular** face that's width × height. On a **triangular** one it's base then perpendicular height, and the unit grid — clipped to the triangle — shows it covering exactly half its rectangle, which is where the ½ comes from. |
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
- **Hub and activity are different places**: the hub is a classroom — a periwinkle wall with daylight from the upper left, white panels, deep-navy type and a dark Challenge shelf — while the activity screens stay cosmic purple. The hub's look is scoped to `#menu` and painted on a viewport-fixed layer inside it (the content column is only 560px wide, so painting it on `#menu` itself would leave the activity's purple showing down both margins), which leaves `body`'s rules untouched. A rendered room can be dropped at `assets/room.png`; it is attached at runtime and only revealed once it decodes, so a missing asset leaves the CSS wall showing rather than a broken image.
- **Ink is measured, not assumed**: colours are sampled from the concept art rather than eyeballed, and ink is chosen by computing contrast against the actual background it will sit on — then verified by sampling the rendered pixel behind each heading. Fill and text accents are separate tokens for a concrete reason: the button blue clears 5.3:1 behind white type but only 2.8:1 as text on the wall, so one shared token would quietly fail.
- **No single overloaded glyph**: the app used one four-pointed sparkle (✦) for everything — section headers, the "there's more here" flourish, the Challenge divider, even a lock replacement — which meant it carried no actual meaning. Each role now has its own mark, several reused deliberately: Home's section label matches its tab icon (⌂), Learn's does the same (⬡) and that hexagon is also what marks "there's deeper content here" everywhere it appears (the Learn-more button, its sheet badge, every interactive block's heading), and the net-mode tag's own ▦ is reused as the Learn card icon for the net sheet specifically, rather than inventing a fourth meaning for the same idea. The five Learn cards get five distinct icons tied to what each sheet actually does, not one icon repeated five times.
- **Guided attention**: a per-face dim overlay lets any moment light exactly one thing. It's an overlay rather than a CSS `filter` because filters cascade into the nested faces.
- **The light is aimed, and Cal walks to it**: one region of interest drives both. The spotlight tracks whatever the step is about — the solid, the flat net, one glowing face, the ruler's drop zone — instead of sitting at mid-stage, and Cal takes the nearest mark that doesn't cover it, so he's beside the thing he's talking about rather than parked in a corner. Both are derived from live geometry, not a per-step/per-shape table of positions, so all six nets and every fold state in between are handled without a lookup that could drift from the shapes. Whether Cal's overlap is judged against the individual faces or their bounding box depends on fold state: a flat cross-shaped net leaves its box's corners genuinely empty, but a folded solid's projected faces overlap each other, so there the silhouette is the honest measure.
- **Parts are tappable**: every polygon side carries a hit bar laid along it, so edges can be found on the solid itself. A shared edge ends up with two bars, which costs nothing — tapping either reveals the set. They sit on the face *node* rather than the face, so the clip-path can't trim them, and 3D depth sorting means a bar round the back sits behind an opaque front face and can't be tapped through the solid.
- **Measuring is derived, not configured**: what the ruler measures is read off the polygon rather than written out per shape, so it can't drift from the geometry. A four-sided face is measured across and down; a triangle's base is its axis-aligned side and its height falls from the opposite vertex, which keeps both passes axis-aligned so one ruler serves both. Spans can be irrational (an equilateral triangle's height is `a·√3/2`), so hashmarks are drawn at whole centimetres and the edge simply ends between two of them.
- **Labels follow the shape**: the face number leans from the centroid toward the *leftmost vertex* — not the bounding-box corner, which can fall outside a clipped triangle, and not the "most top-left" vertex, which on an upward-pointing triangle is the apex at mid-width. The size label hangs toward the face's wide end, so it stays inside whichever way an apex points.
- **Shapes are data** — dimensions, copy and per-step behaviour come from a `SHAPES` config, so adding a solid is mostly a new entry.
