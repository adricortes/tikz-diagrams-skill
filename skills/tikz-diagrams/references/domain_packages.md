# Domain Packages

Before you draw a diagram by hand, look for a domain package in this file. A domain package knows the conventions of its field: circuit symbols, plate notation, commutative arrows, log axes. Hand-drawn TikZ gets these conventions wrong more often, and it takes longer.

Each package has a minimal template in `templates/packages/`. Every template compiles with `xelatex` and passes the visual QA in research mode, except where a note says otherwise. All the packages are part of TeX Live. The list of packages comes from the awesome-tikz list (https://github.com/xiaohanyu/awesome-tikz).

## Choose a package

Use the first row that matches the request.

| Request | Package | Template |
| --- | --- | --- |
| Plot of data or of a function: convergence, error, scaling, timing | `pgfplots` | `pgfplots_convergence.tex` |
| Surface or field over a 2D domain | `pgfplots` | `pgfplots_surface.tex` |
| Sparsity pattern or matrix structure | `pgfplots` | `pgfplots_sparsity.tex` |
| 3D geometry: vectors, angles, coordinate frames | `tikz-3dplot` | `tikz3dplot_vector.tex` |
| Graph with weights or directions, at fixed positions | `tikz-network` | `tikznetwork_graph.tex` |
| Named graph from graph theory: complete, cycle, Petersen | `tkz-berge` | `tkzberge_complete.tex` |
| Graph or workflow with automatic layout | TikZ `graphdrawing` library | `graphdrawing_layered.tex` |
| Tree: decision tree, parse tree, hierarchy | `forest` | `forest_tree.tex` |
| Commutative diagram | `tikz-cd` | `tikzcd_square.tex` |
| Probabilistic graphical model with plates | TikZ `bayesnet` library | `bayesnet_plate.tex` |
| Schedule, timeline, or overlap of tasks per process | `pgfgantt` | `pgfgantt_schedule.tex` |
| Message sequence between processes | `pgf-umlsd` | `umlsd_mpi.tex` |
| Electrical circuit | `circuitikz` | `circuitikz_rc.tex` |

If no row matches, draw the diagram by hand with the families in `references/diagram_patterns.md`.

## Package notes

### pgfplots

Set `\pgfplotsset{compat=1.18}` in every file. Without it, `pgfplots` uses old defaults and writes a warning.

For a figure of a function or of data, use `pgfplots`, because it computes the curve. A hand-drawn curve is only a sketch. This matters for the math/diagram logic review: a `pgfplots` figure of a known function can be `exact`, but a hand-drawn curve is `schematic`.

1. For convergence plots, use `loglogaxis`. Draw the reference rate as a dashed `\addplot` of `C*x^p` over the same domain as the data. Choose `C` so that the reference line sits next to the data and does not hide it.
2. The `ylabel` of a log axis can touch the tick labels, and the QA then reports `text_overlap`. To add space, set `ylabel style={yshift=4pt}`.
3. For a surface, use `\addplot3[surf, shader=interp]` and a perceptual colormap such as `colormap/viridis`. The `sin` and `cos` functions take degrees, so write `sin(deg(pi*x))`.
4. For a sparsity pattern, plot the nonzero entries as `only marks` with `mark=square*`. Set `y dir=reverse` so that row 1 is at the top, as in a matrix.

The sparsity template keeps one `near_edge` warning on the "row" label. The PNG shows that the label is fully visible.

### tikz-3dplot

Call `\tdplotsetmaincoords{<elevation>}{<azimuth>}` before the `tikzpicture`, and add `tdplot_main_coords` to its options. `\tdplotsetcoord{P}{r}{theta}{phi}` defines the point `P` and its projections, for example `Pxy` on the xy-plane. Use the projections for the dashed guide lines.

### tikz-network and tkz-berge

If you give each vertex a position, use `tikz-network`. `\Vertex` and `\Edge` take options for labels, colors, and direction.

Use `tkz-berge` for the named graphs of graph theory, such as `\grComplete`, `\grCycle`, and `\grPetersen`. Set `\GraphInit[vstyle=Normal]` to put each label inside its vertex. The `Classic` style puts labels beside the vertices, where they overlap the edges.

### graphdrawing

The `graphdrawing` library computes the layout, so you do not give positions. It works only with `lualatex`. With `xelatex` or `pdflatex`, the compile fails. Run `compile_render.py` with `--engine lualatex` for these files.

Load the layout algorithm with `\usegdlibrary`. Use `layered` for workflows and dependency graphs, `trees` for trees, and `force` for general graphs.

### forest

Write the tree in bracket notation. The `for tree={...}` option sets the style of all nodes and edges at once. Put a label that contains a comma or a `=` in braces, for example `[{$x_1 < 0.5$}]`.

### tikz-cd

Write the diagram as a matrix: `&` separates columns, and `\\` separates rows. `\arrow[r, "label"]` draws an arrow to the right with a label above it. A `'` after the label puts the label on the other side of the arrow.

### bayesnet

Load it with `\usetikzlibrary{bayesnet}`. The styles `latent`, `obs`, and `const` follow the standard notation of graphical models. `\edge {a, b} {c}` draws arrows from `a` and `b` to `c`. `\plate {name} {(x)(y)} {$N$}` draws a plate around the listed nodes.

### pgfgantt

The chart range is set in time units: `\begin{ganttchart}{1}{12}`. Put two bars on one row to show idle time between them. To show communication or overlap, use a second fill color for one bar.

### pgf-umlsd

The package is old (version 0.6, 2011), and its message labels are fragile. A message label must be plain words. Parentheses, `\texttt`, and `\_` stop the compile. The errors are "A node must have a (possibly empty) label text" and "Use of \mess doesn't match its definition". Braces around the label do not help. Write `send halo`, not `MPI_Isend(halo)`, and put the exact call name in the caption.

A `call` block with an empty return value draws a broken box. Do not use `call` for local work. If you need a call name with symbols, draw the sequence diagram by hand.

### circuitikz

Draw each component as a `to[...]` path segment: `R` for a resistor, `C` for a capacitor, `V` for a voltage source. The option `l=$R$` sets the label.
