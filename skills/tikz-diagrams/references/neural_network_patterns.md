# Neural Network Pattern Map

Use this file for neural network and deep learning diagrams. The templates are in `templates/nntikz/`. They come from NNTikZ by Fraser Love (https://github.com/fraserlove/nntikz, commit `3dc29cf`, MIT license). The license text is in `templates/nntikz/LICENSE`. To copy the templates into another project, copy that file with them.

The local copies have one change from the source. Each file uses `\documentclass[border=5pt]{standalone}` in place of `\documentclass{standalone}`. Without the border, labels touch the page edge, and the visual QA reports `near_edge` on most files.

## Template Map

Start from the template with the same communication job. Each entry gives the file and the diagram family.

### Feedforward networks

- Single neuron or perceptron: `neuron.tex`, family `flow`. Inputs, weights on the edges, a sum node, and an activation.
- Multilayer perceptron: `neural_network.tex`, family `layered-graph`. Two hidden layers, fully connected, with `\vdots` for omitted units.
- Dropout: `dropout.tex`, family `layered-graph`. The same network, with dashed nodes for dropped units.
- Fixed-window sequence model: `neural_sequence_model.tex`, family `flow`. A stack of equations over a window of past inputs.

### Recurrent networks

- RNN, folded and unrolled: `rnn.tex`, family `chain`. The folded cell, an equals sign, and the unrolled chain.
- LSTM cell: `lstm.tex`, family `gated-cell`. Gates, pointwise operators, cell state, and hidden state.
- GRU cell: `gru.tex`, family `gated-cell`. Reset gate, update gate, and the `1-` branch.
- Encoder-decoder (Sutskever et al.): `rnn_encoder_decoder_sutskever.tex`, family `chain`. The context vector feeds the first decoder step.
- Encoder-decoder (Cho et al.): `rnn_encoder_decoder_cho.tex`, family `chain`. The context vector feeds every decoder step.
- Additive attention over a bidirectional RNN: `attention.tex`, family `chain`. Forward and backward states, weights, and a context sum.

### Transformers

- Full transformer: `transformer.tex`, family `stacked-block`. Encoder and decoder stacks, residual paths, and positional encoding.
- Encoder-only stack: `encoder_only.tex`, family `stacked-block`.
- Decoder-only stack: `decoder_only.tex`, family `stacked-block`. Masked attention and shifted input.
- Scaled dot-product attention: `dot_product_attention.tex`, family `flow`.
- Multi-head attention: `multihead_attention.tex`, family `stacked-block`. Shifted copies of each block show the `H` heads.

### Backpropagation

- Gradient through a general node: `backprop/backprop.tex`, family `computation-graph`.
- Local gradient rules: `backprop/backprop_addition.tex`, `backprop_multiplication.tex`, `backprop_elementwise.tex`, `backprop_reshape.tex`, `backprop_max_1.tex`, and `backprop_max_2.tex`. Family `computation-graph`.
- Forward and backward pass on a chain: `backprop/comp_graph.tex`, family `computation-graph`.
- Backpropagation through time: `rnn_backprop.tex`, family `chain`. Losses per step, and dashed gradient paths.
- Gradient flow in an LSTM: `lstm_backprop.tex`, family `gated-cell`.

## Recipes

The templates share one visual vocabulary. Reuse these recipes for a new architecture, so that new figures match the existing ones.

### Shared styles

Every template sets `>=LaTeX` and defines its styles in the `tikzpicture` options. Copy these styles as one set. Do not change one style in only one figure.

```latex
input/.style={circle, minimum width=2.25em, draw, fill=gray!10, thick},
cell/.style={rectangle, rounded corners=2mm, minimum height=2.5em, minimum width=2.5em, draw, thick},
arrow/.style={-latex, thick},
backprop/.style={arrow, dashed, gray},
```

The meaning of each style is fixed:

1. A gray circle (`input`, or `node` in some files) is data: an input, an output, or a prediction.
2. A rounded rectangle (`cell`, `layer`) is a learned transformation.
3. A solid arrow is the forward pass.
4. A dashed gray arrow (`backprop`) is a gradient.

The palette is grayscale only. The figures print in black and white without a loss of meaning.

### Layer of units with omitted units

Place units with `\foreach`, and name each one `<layer>_<index>`. Put `\vdots` between the last listed unit and the final unit, at `pos=0.38` of the path between them. A dashed `cell` with `fit` around the layer marks the layer. A label above the first unit names the layer.

```latex
\foreach \x in {1,...,3}
    \node at (2.5, -\x*1.25) [node] (second_\x) {};
\node at (2.5, -5*1.25) [node] (second_m) {};
\path (second_3) -- (second_m) node[pos=0.38, scale=2] {\vdots};
\node[cell, dashed, fit=(second_1) (second_m)] {};
```

For full connections, use two nested `\foreach` loops over the listed units. Then add the edges to and from the final unit separately.

### Unrolled chain

Use the `chains` library. The `join` option draws the arrow between adjacent cells.

```latex
cellc/.style={cell, on chain, join},
\begin{scope}[start chain=going right, nodes=cellc, arrow, local bounding box=chain]
    \path node (h1) {$\mathbf{h}_1$} node (h2) {$\mathbf{h}_2$} node[xshift=2em] (hT) {$\mathbf{h}_{T}$};
\end{scope}
```

Attach inputs and outputs to every cell with one `\foreach` loop over `{1, 2, T}`. The `local bounding box` gives a name to the whole chain. Use that name to place an equals sign or a folded copy beside the chain.

### Gated cell

Draw the cell as one large rounded rectangle. Put the gates inside it as small `func` boxes, and the pointwise operators as small `op` circles. Fill pointwise elements with `gray!50`.

Route the internal wires with the orthogonal path operators `|-` and `-|`. Use `rounded corners` on those wires. For a wire that starts at the height of one node and the column of another node, use the intersection coordinate `(ht-1 -| sigf)`.

### Stacked block with residual paths

Place each sub-layer as a `layer` node with a fixed `text width`. Draw each residual path in three segments: below the sub-layer, out to the side, and up into the `Add & Norm` node.

```latex
\draw[arrow] (attn1.south)++(0,-0.6) -| ($(add1.west)+(-0.6,-0.5)$) |- (add1.west);
```

To draw the repeated block, draw a `block` node with `fit` on the background layer. The `backgrounds` library supplies that layer. Put `$N\times$` beside the block.

### Positional encoding symbol

The `sin wave` style draws a sine curve inside a circle with `path picture`. Copy the `do path picture` and `sin wave` styles from `transformer.tex` together, because the second style depends on the first.

### Gradient arrows

Draw each gradient as a `backprop` arrow in the direction opposite to the forward arrow. On a straight forward edge, draw the forward arrow with `+(0cm,0.1cm)` and the gradient arrow with `+(0cm,-0.1cm)`. Then the two arrows run in parallel and do not overlap. On a curved edge, use `bend left` or `bend right`. Write the gradient as a bar over the variable, for example `$\bar{\mathbf{t}}_1$`.

## Visual QA Notes

The visual QA checker gives false positives on these templates. Find the cause before you change a figure.

1. Math accents, primes, and fractions split into several PDF text boxes. Examples are `\overrightarrow{\mathbf{h}}`, `\mathbf{h}'_{T'}`, and `\frac{\partial g}{\partial t}`. The checker then reports `text_overlap` inside one label. Look at the PNG. If the label reads correctly, record the finding as a false positive.
2. These figures have no title. The checker can take the top label as a title and report `title_band_collision` on the labels near it. This occurs on most backprop templates. If the figure has no title, record the finding as a false positive.
3. A two-line label at normal line spacing triggers `text_overlap`, for example "Feed" over "Forward". The `layer` nodes use `\vspace{-0.05cm} \linebreak`, but a plain `\\` gives the same finding. In the PNG, the two lines do not touch. In a new figure, write the break as `\\[2pt]` in a node with `align=center`. The 2pt of extra space makes the check pass.

After these three checks, the remaining findings are real. On the source files, the `near_edge` warnings on `backprop_elementwise.tex` and on the two encoder-decoder files are real but small.
