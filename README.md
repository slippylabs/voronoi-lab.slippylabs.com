# Voronoi & Delaunay Lab

Place points and watch the Delaunay triangulation and its dual Voronoi diagram build themselves — with Lloyd relaxation, area colouring, a live correctness check, and SVG export. Runs entirely in your browser.

**Live:** <https://voronoi-lab.slippylabs.com/>

## What it does

- Click to add a site, drag to move one, shift-click to remove it.
- Scatter patterns including a perfect grid, which is degenerate on purpose — every four neighbours are co-circular, and that is where naive triangulators fall over.
- Lloyd relaxation: every site jumps to the centre of its own cell. Repeat it and a random scatter becomes a honeycomb.
- Colour cells by area or by number of sides, overlay the triangulation and the circumcircles, and export SVG or PNG.

## How it works

Bowyer–Watson incremental insertion for the triangulation: add a point, delete every triangle whose circumcircle contains it, retriangulate the hole.

Voronoi cells are built as an **intersection of half-planes** — start with the rectangle, cut with the perpendicular bisector between the site and each Delaunay neighbour. Cutting against neighbours alone is sufficient because a Voronoi edge only ever separates Delaunay neighbours, and the page verifies that shortcut live against the brute-force version that cuts against every other site.

## Verification

- The **empty-circumcircle property** — no site inside any triangle's circumcircle — is the definition, is exactly checkable, and is rechecked after every change on the page itself.
- The triangulation matches Qhull (`scipy.spatial.Delaunay`) on points in general position; where the input is degenerate and the triangulation genuinely is not unique, both answers are required to be valid and cover the same hull.
- Cells are compared against an independent brute-force reference, must tile the rectangle exactly, and must each contain their own site.
- Lloyd relaxation is checked to reduce the spread of cell areas monotonically (measured: 0.59 → 0.09 from a uniform start) and to leave 70% of interior cells hexagonal.

One thing worth knowing: comparing two correct Voronoi cells by their vertex lists does not work. The brute-force pass inserts a collinear vertex wherever a co-circular site's bisector touches an edge — same polygon, one more corner — so the comparison drops collinear vertices first.

## Run it locally

A static site. No build step, no package manager, no dependencies:

```
git clone git@github.com:slippylabs/voronoi-lab.slippylabs.com.git
cd voronoi-lab.slippylabs.com
python3 -m http.server 8000
```

Then open <http://localhost:8000>.

---

Part of [Slippy Labs](https://slippylabs.com). Every tool is indexed at
[projects.slippylabs.com](https://projects.slippylabs.com).
