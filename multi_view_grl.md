**Contrastive Multi-View Representation Learning on Graphs** by Hassani and Khasahmadi, 2020.

Paper can be found [here](https://arxiv.org/pdf/2006.05582.pdf).

## The Problem
**tldr:** Introduces a new (SOTA) self-supervised method to learn node and graph representations 
with multiple structural views to perform node/graph classification. It performs similarly to other supervised node/graph classification methods.

Node and graph classifications are often approached via unsupervised learning that are either reconstruction-based or use contrastvie learning.
Recent SOTA results for unsupervised techniques use contrastive learning to increase the mutual information between node and graph representations.
These are however limited by requiring special encoder functions.

Here, they propose to perform contrastive learning on differen structural views on graphs.

## Related Work

Deep graph InfoMax (DGI) (Velikovi et al, 2019): maximizes mutual information between
different patch representations centered around a given ndoe and corresponding high-level summaries of the graph.in

InfoGraph (Sun et al, 2020): maximizes mutual information between graph-level representations and the
representations of substructures of different scales (e.g., nodes, edges, triangles).

Graph Diffusion Networks (Klicpera et al, 2019): they use graph diffusion to allow messages to pass through higher-order neighborhoods at each massage passing step (layer).
This is in contrast to how commonly used GNNs perform message-passing, where only one-hop neighbors' messages are passed at a time.

MI maximization: various methods (CPC, DIM, CMC, AMDIM) that are all based upon the idea that an encoder function should be such that it maximizes the mutual information between the learnt representation and the input.

## Proposed Approach

1. Data augmentation: using graph diffusion, they sample graph and create a different view on the same graph that has high correlation with the original.

2. A GNN for each graph view, followed by a shared MLP, outputting node representations.

3. A graph pooling layer followed by a shared MLP using node representations to output graph representations

4. Use discriminator to contrast node representation from one view with a graph representation from another view.

## Experiments

## Thoughts
