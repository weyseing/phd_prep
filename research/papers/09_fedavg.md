# Field 9: Federated / Privacy-Preserving AI
**Paper:** Communication-Efficient Learning of Deep Networks from Decentralized Data (FedAvg) (2017 — McMahan et al.) — https://arxiv.org/abs/1602.05629

## TL;DR

Train one global neural net across thousands of devices that each refuse to share their raw data. The server broadcasts a model to a random sample of clients each round, lets each run several local SGD epochs on its private data, then averages the returned weights — weighted by local dataset size. The trick is doing meaningful local work *between* communication rounds, cutting rounds 10x–100x vs naive synchronous SGD. The paper founded federated learning, shipped inside Google's Gboard, and remains the default baseline in every privacy-preserving ML paper since.

## What it does (the mechanism)

The setup: K clients (mobile phones, in the original framing; hospitals, banks, browsers in the modern one) each hold a local dataset that never leaves the device. A central server holds the global model w. At each round t, the protocol is:

1. **Server samples** a fraction C of the K clients (e.g. C=0.1 means 10% of clients participate that round).
2. **Server broadcasts** the current model weights w_t to those selected clients.
3. **Each client runs** E local epochs of SGD on its private data, with local minibatch size B, producing updated weights w_t+1^k.
4. **Clients upload** their updated weights (not gradients, not raw data — just the trained weights).
5. **Server aggregates** by taking a weighted average: w_t+1 = sum_k (n_k / n) * w_t+1^k, where n_k is the size of client k's dataset and n is the total.

Three hyperparameters control the compute-vs-communication trade-off: **C** (client fraction per round), **E** (local epochs), **B** (local batch size). The corner case E=1, B=infinity is just synchronous large-batch SGD — the paper calls this **FedSGD** and uses it as the baseline. The contribution is **FedAvg**: E > 1, so each client does substantial local work between rounds.

The surprising claim is that *averaging weights of non-convex neural networks works at all*. Figure 1 shows that with independent initializations, averaging is catastrophic. But when both clients start each round from the *same* shared weights w_t (which is exactly how FedAvg works), parameter-space averaging stays inside one loss basin and the averaged model performs *better* than either parent. The whole algorithm rests on this empirical observation about over-parameterized loss surfaces.

## Why it matters

Founded the field of federated learning. The motivation was concrete: keystrokes, photos, medical records are too sensitive to centralize, but you still want to train models on them. FedAvg gave a clean primitive — "train without moving the data" — deployable in production. Google's Gboard predictive keyboard was the first large-scale deployment; Apple, Meta, and the hospital-AI ecosystem followed.

Downstream, the paper spawned a research area: DP-FedAvg, secure aggregation (Bonawitz et al.), federated transfer learning, cross-silo federated for hospitals, personalized federated, federated foundation models. The framing — non-IID + unbalanced + communication-constrained + massively distributed — defined "federated optimization" as a problem class distinct from classical distributed optimization.

## Key results

- **MNIST CNN, IID**: FedAvg reaches 99% accuracy in 18 rounds vs. 626 for FedSGD — a **34.8x** reduction.
- **MNIST CNN, pathological non-IID** (each client sees only 2 digit classes): still **2.8x** reduction — averaging works even when client distributions are wildly disjoint.
- **CIFAR-10**: 85% accuracy in 2,000 rounds for FedAvg vs. 99,000 minibatch updates for centralized SGD — roughly **49x** fewer communication rounds.
- **Shakespeare character LSTM, non-IID by speaking role**: **95.3x** speedup. The unbalanced realistic split was actually *easier* than the IID balanced one, because some clients had enough data for local training to be informative.
- **Large-scale next-word LSTM** (10M social-network posts, 500k clients): **23x** fewer rounds than FedSGD.

FedAvg also converges to *higher* accuracy than FedSGD in most settings — the authors conjecture an implicit dropout-like regularization from the averaging.

## Vocabulary glossary

- **Federated learning**: training a shared model across decentralized clients without moving their raw data.
- **FedAvg**: the algorithm in this paper — average client weights weighted by local dataset size, with multiple local epochs per round.
- **FedSGD**: the E=1 corner case — equivalent to synchronous large-batch SGD across clients.
- **Client / server**: clients hold private data and compute local updates; the server orchestrates rounds and aggregates.
- **Local epochs E**: number of passes a client makes over its local data per round. Higher E = more local compute, fewer rounds, but risk of client drift.
- **Client fraction C**: fraction of K clients sampled per round.
- **Communication rounds**: the bottleneck metric — uplink bandwidth (~1 MB/s on mobile) dominates wall-clock cost.
- **Non-IID data**: client distributions differ from the population — pathological case: each client sees only one or two classes.
- **Unbalanced data**: some clients have far more local examples than others.
- **Focused collection / data minimization**: privacy principle that you only collect what you need; FedAvg embodies it.
- **Secure aggregation**: cryptographic protocol so the server sees only the *sum* of client updates, never individual ones.
- **Differential privacy**: formal privacy guarantee via calibrated noise injection — natural complement to FedAvg.

## Limitations / honest take

FedAvg degrades sharply on highly non-IID data — follow-ups (FedProx, SCAFFOLD, FedNova) fix client drift. No formal privacy guarantee — averaged weights can leak training data via gradient-inversion; you need DP-SGD on top. Vulnerable to malicious clients (model poisoning, backdoors) — Byzantine-robust aggregation came later. Communication still expensive for billion-parameter models — gradient compression followed. Synchronous-round assumption breaks at scale (stragglers); async is harder. Model homogeneity is assumed — all clients run the same architecture, which fails for heterogeneous devices.

## Fit to my proposal

**Verdict:** not relevant (parking-lot citation).

Honest assessment: Reddit and Twitter/X mental-health data is *publicly accessible*. There is no privacy-preserving training scenario where FedAvg helps. The data lives on a public platform; pulling it via API (or archive dumps like Pushshift) is the standard, IRB-blessed procedure. Federated learning solves a problem the proposal does not have. Adding a federated angle just to look modern would be padding.

Where it could become relevant:
1. **If the proposal pivots or extends to clinical mental-health text** — hospital EHR notes, therapy session transcripts, crisis-line chat logs — where data legally cannot leave the institution. Then FedAvg + DP-SGD is the obvious privacy baseline and would deserve a methods chapter.
2. **If a target supervisor's lab works on healthcare federated learning specifically.** Some Sunway, Monash, and Nottingham faculty have this focus. A "federated clinical extension" could be a Year-4 deliverable that turns the proposal into a sweetener for that supervisor without changing the core JEPA world-model contribution.
3. **Cross-platform federated** — train a discourse model jointly across Reddit, Twitter, and Discord without centralizing user data. Conceptually appealing, but the platforms don't share weights anyway and have no business reason to cooperate. Moot in practice.

**Recommendation:** cite FedAvg only if (a) a target supervisor's research demands a federated angle, or (b) the proposal explicitly extends to clinical data. Otherwise drop. Privacy and ethics commentary about scraping Reddit belongs in the proposal's "ethics & data" section as a paragraph, not as a methodological pillar. Don't dress up a public-data project in privacy-preserving clothing it doesn't need.
