# HPC and Scientific Computing Pattern Map

Use this file for computer architecture, interconnect topologies, parallel algorithms, data pipelines, timing, and computational grids. The templates are in `templates/texample/`. They come from TeXample.net (https://texample.net/), and each file has a different author.

## License

These templates use the CC BY-SA 4.0 license, not the MIT license of the rest of this skill. `templates/texample/LICENSE.md` explains the two conditions. In short:

1. A figure made from one of these files needs a credit line with the author and the source page. The first line of each file gives both.
2. If you change a file and share it, the changed file must also use CC BY-SA 4.0.

For a paper, put the credit in the caption or the acknowledgments. To copy a file, keep its header lines.

## Engines

All files compile with `xelatex`, except `spherical-and-cartesian-grids.tex`. Compile that file with `pdflatex`, because with `xelatex` the shaded sphere renders solid black. The header of each file names any other local change.

## Status labels

The labels have the same meaning as in `references/graph_ml_patterns.md`: `clean`, `qa-false-positive`, `small-text`, and `defect`.

## Template Map

### Interconnects and network topologies

- Data center network in tiers: core, aggregation, access, and servers. `network-topology.tex`, `clean`. The labels are small, so give the figure the full text width.
- Hypercube: a 4-cube with Gray code labels. `gray-code-in-4-cube.tex`, `clean`.
- Pancake graph: `pancake-network.tex`, `qa-false-positive`.
- Star graph: `star-graph.tex`, `qa-false-positive`.
- 3D grid graph on stacked planes: `3d-graph-model.tex`, `clean`. Use it for a 3D mesh, a torus slice, or a stencil neighborhood.

### Architecture and memory

- Cache coherence protocol (MESIF) as a state machine: `mesif.tex`, `clean`. Use it as a model for MESI, MOESI, or any protocol with states and transitions.
- Host and clients around a central computer: `computer-diagram.tex`, `qa-false-positive`.
- Hardware timing diagram with clock, signals, and buses: `timing-diagram.tex`, `qa-false-positive`. Use it for pipeline stages or for the latency of a memory access.
- Timing of latches and flip-flops: `more-tikz-timing-examples.tex`, `clean`. This file has two pages. It uses the `tikz-timing` package.

### Parallel algorithms and data movement

- Matrix product with one row of `A` and one column of `B` in color: `matrix-multiplication.tex`, `qa-false-positive`. Use it as a start for blocked or tiled matrix multiplication.
- Transpose with the moved block in color: `highlighting-matrix.tex`, `qa-false-positive`. Use it for data layout, row-major against column-major, or a transpose in a parallel FFT.
- Recursion tree of merge sort, with the cost of each level: `merge-sort-recursion-tree.tex`, `qa-false-positive`. Use it for divide-and-conquer algorithms and their work per level.
- Data acquisition pipeline: electronics, DAQ, buffer, storage, and monitor. `data-flow-diagram.tex`, `qa-false-positive`. Use it for an I/O pipeline or an in-situ analysis workflow.
- Sequence of calls between processes, with activation bars: `sequence-diagram.tex`, `qa-false-positive` and `small-text`. For MPI message patterns with symbols in the labels, use this file, because `pgf-umlsd` cannot show those symbols.

### Computational grids and model coupling

- Spherical grid and Cartesian grid patches on a sphere: `spherical-and-cartesian-grids.tex`, `clean` with `pdflatex`.
- Nested grids for model coupling, with an inner and an outer grid: `nested-grids-in-swan-and-wam-coupling.tex`, `qa-false-positive`.
- Stacked grids of a wave model: bathymetry, wind, friction, and the computational grid. `swan-wave-model.tex`, `clean`. Use it for any model with several fields on one domain.
- Level-set function values on a tilted grid: `contours-grids.tex`, `small-text` and `defect`. The arrow tip of the $\phi_\Omega(s)$ label covers one grid value. Move the arrow before you use the figure.

### Simulation methods

- Simulation approaches against levels of abstraction: discrete event, agent based, and system dynamics. `simulation-abstraction.tex`, `qa-false-positive`.

## Recipes

### Stacked planes for fields and layers

Two templates draw layers as slanted grids, with two different methods:

1. `swan-wave-model.tex` slants each grid with the `xslant` and `yslant` options.
2. `3d-graph-model.tex` defines one macro that calls `\pgftransformcm` inside a `scope`. Every plane uses the same macro, so all planes stay aligned. To show which edge is in front, the file draws each edge twice: first as a thick white line, then as the normal line on top of it.

Put each new field on a new plane with a vertical offset. Label each plane at its right edge, with a short line to the plane. This keeps the labels out of the grids.

### Highlighted entries in a matrix

The two matrix templates use different methods:

1. `highlighting-matrix.tex` marks the first and the last entry of a block with `\tikzmark`, inside a normal `array`. Then a node with `fit`, `overlay`, and `remember picture` draws a rounded box with `fill opacity=0.5` around the block.
2. `matrix-multiplication.tex` builds each matrix with `matrix of math nodes`. A circle style marks the entries of one row of `A`, one column of `B`, and the result entry of `C`. Blue lines connect the row and the column to the result.

Method 1 reads positions from the `.aux` file of the previous LaTeX run. After one run, the boxes and arrows are in the wrong place. `scripts/compile_render.py` runs LaTeX again until the `.aux` file does not change, so it gives the correct figure. If you compile by hand, run LaTeX twice.

### Protocol state machines

`mesif.tex` draws each state as a circle and each transition as a curved arrow with a label. The arrows use `bend left` with different angles, from 30 to 65 degrees, so that parallel transitions do not overlap. Self-loops use `loop above` and `loop right`.

## Gaps

These figures do not cover domain decomposition with ghost (halo) cells, the memory hierarchy as a pyramid, roofline plots, or strong and weak scaling plots. For a roofline or scaling plot, use the `pgfplots` templates in `references/domain_packages.md`. For the other topics, draw by hand from the recipes above.
