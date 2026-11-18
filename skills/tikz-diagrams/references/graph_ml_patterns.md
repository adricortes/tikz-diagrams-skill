# Graph and Machine Learning Pattern Map

Use this file for graph neural networks, graph algorithms, convolutional networks, generative models, reinforcement learning, and probabilistic sequence models. The templates are in `templates/petarv/`. They come from the TikZ collection of Petar Veličković (https://github.com/PetarV-/TikZ, commit `86aee04`, MIT license). The license text is in `templates/petarv/LICENSE`. To copy the templates into another project, copy that file with them.

This is a selection of 45 of the 65 source figures. The selection leaves out signal processing, bioinformatics, and emulator figures. It also leaves out the MLP, LSTM, and dropout figures, because `templates/nntikz/` already covers them. For other neural network architectures, read `references/neural_network_patterns.md`.

The local copies have one change from the source. Each file uses `\documentclass[crop,tikz,border=5pt]{standalone}`. All 45 files compile with `xelatex`.

## Status labels

Each entry has one of these status labels:

1. `clean`: the visual QA passes, and the PNG shows no defect.
2. `qa-false-positive`: the visual QA fails, but the PNG shows no defect. The section "Visual QA Notes" gives the causes.
3. `small-text`: the text is too small for a slide. In a paper at full column width, the text is readable. For a slide, increase the font size or split the figure.
4. `defect`: the PNG shows a real defect. Repair the defect before you use the figure.

## Template Map

### Graph neural networks

- Graph convolution over a neighborhood: `graph_convolution.tex`, `clean`.
- Graph attention (GAT) layer, with attention coefficients on the edges: `gat_layer.tex`, `clean`.
- Message passing neural network: `message_passing_neural_network.tex`, `qa-false-positive`.
- Deep Graph Infomax: `deep_graph_infomax.tex`, `qa-false-positive`.
- Deep Graph Infomax on a sparse graph: `sparse_dgi.tex`, `clean`.
- Hierarchical graph classifier with pooling: `hierarchical_graph_classifier.tex`, `qa-false-positive`.
- Relational network over objects and a query: `relational_network.tex`, `qa-false-positive`.
- Object extraction for a relational network: `rn_object_extraction.tex`, `clean`.
- Semi-supervised embedding: `semi-supervised_embedding.tex`, `clean`.

### Graphs and graph algorithms

- Maximum flow, with flow and capacity on each edge: `maximum_flow_problem.tex`, `clean`.
- Shortest path, with the path in color: `shortest_path_problem.tex`, `clean`.
- Hamiltonian cycle: `hamiltonian_graph.tex`, `clean`.
- De Bruijn graph: `de_bruijn_graph.tex`, `clean`.
- Directed web graph with self-loops: `web_graph.tex`, `clean`.
- Markov clustering (MCL), clusters in color: `mcl.tex`, `clean`.
- Multilayer network with its tensor form: `multilayer_network.tex`, `qa-false-positive`.
- Multiplex network on two planes: `multiplex_epidemics-awareness_network.tex`, `qa-false-positive`.
- Underlying graph of a multiplex network: `multiplex_network_underlying_graph.tex`, `defect`. The wavy edges hide the closing parenthesis of the labels in the middle row.

### Convolutional networks and cross-connections

- 2D convolution of an image with a kernel: `2d_convolution.tex`, `defect`. The kernel weights inside the highlighted region collide with the matrix digits.
- Dilated (à trous) convolutions: `a_trous_convolutions.tex`, `clean`.
- Convolutional autoencoder as 3D blocks: `convolutional_autoencoder.tex`, `small-text`.
- Cross-connections between two streams: `convolutional_cross-connection.tex`, `fully-connected_cross-connection.tex`, and `1d-2d_cross-connection.tex`, all `clean`.
- Cross-modal CNN: `x-cnn.tex`, `qa-false-positive` and `small-text`.
- Cross-modal LSTM: `x-lstm.tex`, `qa-false-positive` and `small-text`.

### Generative and representation models

- Generative adversarial network: `generative_adversarial_network.tex`, `qa-false-positive`.
- CycleGAN: `cyclegan.tex`, `qa-false-positive`.
- Variational denoising autoencoder: `variational_denoising_autoencoder.tex`, `clean`.
- Deep belief network: `deep_belief_network.tex`, `clean`.
- Self-attention: `self-attention.tex`, `clean`.
- Bidirectional LSTM: `bidirectional_long_short-term_memory.tex`, `clean`.
- Progressive neural network for two tasks: `progressive_neural_network.tex`, `qa-false-positive`.

### Learning setups and reinforcement learning

- Supervised learning setup: `supervised_learning_setup.tex`, `qa-false-positive`.
- Semi-supervised learning with a decision boundary: `semi-supervised_learning.tex`, `clean`.
- Greedy policy over states: `reinforcement_learning_greedy_policy.tex`, `qa-false-positive`.
- A3C execution with a global state and agents: `a3c_execution.tex`, `qa-false-positive`.
- A3C network with policy and value heads: `a3c_neural_network.tex`, `qa-false-positive`.

### Probabilistic sequence models

- Gaussian mixture hidden Markov model: `gmhmm.tex`, `clean`.
- HMM transition smoothing: `hmm_transition_smoothing.tex`, `qa-false-positive`.
- Multiplex chain GMHMM: `multiplex_chain_gmhmm.tex`, `qa-false-positive` and `small-text`.

### Computer architecture and workflows

- Fetch-decode-execute cycle: `fetch-decode-execute_cycle.tex`, `qa-false-positive`.
- Execution of one instruction on a CPU and memory: `instruction_execution.tex`, `clean`.
- Data flow between git areas: `git_dataflow.tex`, `qa-false-positive`.
- Polar coordinates in a circle: `coordinate_systems.tex`, `clean`.

## Recipes

### Wavy edges for message passing

22 of the 45 figures load `decorations.pathmorphing`, most of them for wavy edges. A wavy edge shows a message, a learned transformation, or a stochastic step. A straight edge shows a fixed connection. Keep this meaning in a new figure.

```latex
\usetikzlibrary{decorations.pathmorphing}
\draw[->, decorate, decoration={snake, amplitude=0.4mm, segment length=2mm, post length=1mm}] (a) -- (b);
```

The `post length` keeps the end of the edge straight, so that the arrow tip has a clean direction. A wavy edge is wider than a straight edge. Keep labels at least 3mm away from it, because a wavy edge can hide part of a label. The defect in `multiplex_network_underlying_graph.tex` is an example.

### Planes for layers of a network

`multiplex_epidemics-awareness_network.tex` and `convolutional_cross-connection.tex` draw each layer on a slanted plane. The plane is a filled parallelogram, and each layer has its own color. The edges between the planes show the coupling of the layers. Use this recipe for multiplex networks, multi-stream models, and coupled systems.

### Colored node groups

Graph figures show structure with color: a cluster, a path, or a neighborhood. Use one color for each group, and keep the other nodes gray or white. Some of these figures use red and green as a pair, for example in `mcl.tex`. Readers with red-green color blindness cannot tell such a pair apart. In a new figure, pair red with blue, or vary the shape as well as the color.

## Visual QA Notes

The false positives on these figures come from the causes in `references/neural_network_patterns.md`: math accents such as `\vec{h}`, and figures without a title. Two more causes occur here:

1. A node with a large letter and a small caption inside it, as in `fetch-decode-execute_cycle.tex`, gives `text_overlap` between the two lines.
2. A label drawn twice, for example once for a shadow and once on top, gives `text_overlap` between the two copies. `x-lstm.tex` does this.

The visual QA does not detect text under a line or a wavy edge. `multiplex_network_underlying_graph.tex` has this problem, but it passes the visual QA. The QA does flag `2d_convolution.tex`, but its 39 findings look like the false positives for math text. Always inspect the PNG.
