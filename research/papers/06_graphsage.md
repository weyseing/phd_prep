# Field 6: GNN / Network Science
**Paper:** Inductive Representation Learning on Large Graphs (GraphSAGE) (2017 — Hamilton, Ying, Leskovec) — https://arxiv.org/abs/1706.02216

## TL;DR

GraphSAGE dragged graph neural networks out of the toy-benchmark phase and into production. The trick is small but operationally enormous: instead of learning a fixed embedding per node (transductive — you must retrain when the graph grows), learn a *function* that produces an embedding from a node's features and a sampled neighborhood. That function — a stack of trainable aggregators over K-hop neighbors — generalizes to nodes the model has never seen, and even to entirely new graphs. For evolving social systems like Reddit, Twitter, or Pinterest, where new users and posts arrive every second, this is the difference between "research artifact" and "deployable component."

## What it does (the mechanism)

The algorithm is an iterated neighborhood aggregation. Each node v starts with its raw feature vector h_v^0 (text embedding, profile features, whatever). At each layer k = 1...K:

1. **Sample neighbors.** Draw a fixed-size random subset N(v) from v's neighbors — say 25 at layer 1, 10 at layer 2. Sampling caps compute regardless of how high-degree v is, and gives an implicit form of regularization.
2. **Aggregate.** Combine the layer-(k-1) embeddings of the sampled neighbors into a single vector h_{N(v)}^k via an *aggregator function*. The paper proposes three: **mean** (elementwise average — close to GCN), **LSTM** (run an LSTM over a random permutation of neighbors — more expressive but order-sensitive in a hacky way), and **max-pool** (each neighbor passes through a small MLP, then elementwise max).
3. **Combine and transform.** Concatenate h_v^{k-1} with h_{N(v)}^k, multiply by a trainable weight matrix W_k, apply a nonlinearity (ReLU), and L2-normalize. That gives h_v^k.

After K layers, h_v^K is the node's embedding, encoding information from its K-hop neighborhood. K is usually 2.

The aggregators and W_k matrices are *learned*, which is the inductive leap. The model can be trained two ways: **supervised** (cross-entropy on node labels), or **unsupervised** with a graph-based loss — encourage embeddings of nodes that co-occur on short random walks to be similar (negative-sample distant nodes), the same word2vec-style objective DeepWalk and node2vec use, but now wrapped around a parameterized encoder rather than a lookup table.

At test time, embedding a brand-new node — or a whole new graph with the same feature schema — just means running the forward pass with the trained weights. No retraining, no re-factorization. Kipf-Welling's GCN is essentially a special case (mean aggregator with a particular normalization), but the original GCN was framed transductively over a fixed Laplacian.

## Why it matters

GraphSAGE is the bridge between graph representation learning as a spectral / matrix-factorization curiosity and graph representation learning as a deployable component. Production graphs — Reddit, Pinterest, Twitter, Amazon — add millions of nodes per day; transductive methods (DeepWalk, node2vec, vanilla GCN) require retraining or awkward extensions for every new node. Hamilton et al. showed that an inductive parameterized encoder works as well or better while being deployable.

It also planted theoretical roots: the paper sketches a connection between K-layer aggregation and the Weisfeiler-Lehman graph isomorphism test, which later work (GIN) made rigorous. Practically, GraphSAGE is the direct ancestor of **PinSAGE** (Pinterest's billion-node recommender), GraphSAINT, Cluster-GCN, and the whole sampling-based GNN literature. Almost every "GNN at scale" paper since 2018 cites it.

## Key results

Three benchmarks, all with strict inductive splits — train on one set of nodes, test on completely unseen nodes (or unseen graphs):

1. **Citation networks** (Web of Science): node classification across held-out nodes. GraphSAGE beats DeepWalk by roughly 50% relative F1 — DeepWalk literally cannot embed unseen nodes, so this is partly tautological, but it makes the inductive point sharply.
2. **Reddit posts**: predict which subreddit a post belongs to, given a co-comment graph (posts linked if the same user commented on both). The GraphSAGE-LSTM aggregator wins, with mean and max-pool close behind. ~230k nodes — large for 2017.
3. **Protein-protein interaction (PPI)**: train on a set of tissue-level PPI graphs, test on entirely new graphs. Big margin over node2vec / DeepWalk baselines.

The headline isn't the absolute numbers; it's that an inductive method matches or beats transductive baselines while generalizing to unseen nodes/graphs.

## Vocabulary glossary

- **Inductive vs transductive embedding**: inductive = function generalizes to new nodes; transductive = embedding only valid for nodes seen at training.
- **Neighborhood sampling**: drawing a fixed-size random subset of a node's neighbors per layer to bound compute.
- **Aggregator**: the function (mean / LSTM / max-pool) that combines neighbor embeddings into one vector.
- **Message passing**: the general framing — nodes send messages (their embeddings) along edges; each node aggregates incoming messages.
- **GCN as special case**: GraphSAGE with mean aggregator and symmetric normalization recovers Kipf-Welling GCN.
- **K-hop receptive field**: K stacked layers means each node sees information from up to K hops away.
- **Weisfeiler-Lehman test**: classical graph-isomorphism heuristic; GNN expressivity is bounded by it.
- **Random-walk co-occurrence loss**: word2vec-style unsupervised objective — short random walks define "context" for nodes.
- **node2vec / DeepWalk**: pre-GNN transductive baselines using random walks + skip-gram.
- **PinSAGE**: Pinterest's GraphSAGE descendant, ~3 billion nodes.

## Limitations / honest take

Random neighborhood sampling introduces variance — embeddings for the same node differ across runs. Fixed sample size discards information for high-degree nodes (a hub with 10,000 neighbors gets the same 25-sample treatment as a node with 30). Mean aggregation is order-invariant but underexpressive: it cannot distinguish certain non-isomorphic neighborhoods, which **GAT** (attention-weighted) and **GIN** (sum + MLP) later fixed. The LSTM aggregator is order-sensitive in an unprincipled way. No native handling of **edge features** or **heterogeneous graphs** — R-GCN and HGT came later. And critically: GraphSAGE assumes a *static* graph; temporal dynamics (TGN, TGAT, ROLAND) needed a separate line of work.

## Fit to my proposal

**Verdict:** supporting angle (foundational reading).

Concrete use: if the world-model encoder needs to embed Reddit's user-reply or post-comment graph — which is *constantly* adding new users and posts — GraphSAGE is the canonical inductive-GNN reference, and the paper literally evaluates on Reddit (the same platform as my MSc thesis). That's a strong citation lever in the proposal: "the original inductive GNN paper validated on the very platform we're modelling."

But: GraphSAGE is from 2017. The actual building blocks would be modern descendants — **GAT** for attention-weighted aggregation, **TGN/TGAT** for temporal evolution, **R-GCN** for heterogeneous edge types (post→reply vs user→post), **GraphSAINT** for scalable training. So GraphSAGE goes in the related-work section, not in the architecture diagram.

**Honest positioning question:** does the proposal *need* graph encoding at all? Two paths:

1. **Sequence-only.** Treat each subreddit as a multivariate time series of aggregate features (post counts, sentiment distribution, topic mix). JEPA-style latent dynamics on top. No graphs. Simpler, more tractable, directly continues the MSc.
2. **Graph-aware.** Model user-user reply structure and propagation explicitly. Richer (you can capture influencer dynamics, cascades) but much more complex and compute-heavy.

**Recommendation:** start with (1) as the Year-1 thesis core. Introduce the GNN encoder as a Year-2 or Year-3 extension — "world model with explicit propagation structure." Don't bite off graphs in Year 1. When that extension lands, GraphSAGE is the foundational citation; TGN is the actual encoder.
