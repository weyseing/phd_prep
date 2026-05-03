# Field 3: Causal AI / Counterfactual Reasoning
**Paper:** Estimating Counterfactual Treatment Outcomes over Time Through Adversarially Balanced Representations (CRN) (2020 — Bica, Alaa, Jordon, van der Schaar) — https://arxiv.org/abs/2002.04083

## TL;DR

CRN is a seq2seq RNN that estimates counterfactual treatment outcomes over time from observational data. Its trick: at every timestep it uses domain-adversarial training (gradient reversal) to build a representation of patient history that is *predictive of future outcomes* but *invariant to the current treatment*. This strips time-varying confounder bias without computing inverse-probability-of-treatment weights (IPTW). It is the first scalable neural method for longitudinal counterfactuals and the conceptual ancestor of Causal Transformer, G-Net, and TE-CDE.

## The problem (sequential causal inference)

Clinicians choose treatments based on patient history. That makes patient covariates **time-varying confounders**: features simultaneously affected by past treatment *and* driving future treatment assignment. A naive supervised model predicting P(Y_{t+1} | X_t, A_t, history) inherits the doctor's policy and misestimates what happens under a *different* policy. Classical epidemiological fixes — g-formula, Structural Nested Models, especially **Marginal Structural Models (MSMs)** with **IPTW** — re-weight the data so treatment is independent of confounders in the pseudo-population. But IPTW is fragile: weights blow up when treatment probability is small, estimators have huge variance, and everything depends on a correctly specified propensity model. Stacked over many timesteps, errors compound. Neural extensions like RMSN (Lim et al., 2018) bolt IPTW onto an RNN and inherit the same instability. CRN's pitch: throw IPTW out, balance in *representation space* instead.

## What it does (the mechanism)

CRN is an encoder–decoder, both halves LSTMs. The **encoder** consumes the patient trajectory — covariates X_1..t, past treatments A_1..t-1, baseline V — and at each step produces a hidden representation Φ_t. Two heads sit on top:

1. **Outcome head G_y**: takes (Φ_t, A_t) and predicts Y_{t+1}.
2. **Treatment classifier G_a**: takes Φ_t and tries to predict A_t. A **gradient reversal layer (GRL)** sits between Φ_t and G_a, negating the gradient on the backward pass. The encoder is thus pushed to make Φ_t *un*informative about A_t while G_a tries its best.

This is a direct lift of Ganin et al.'s DANN, generalized via Sebag et al. (2019) from binary domain adaptation to the multi-treatment case — the "domains" are the K treatment categories at time t. At equilibrium, P(Φ_t | A_t = A_1) = ... = P(Φ_t | A_t = A_K), the balancing condition Robins (1999) showed is sufficient for unbiased counterfactual estimation.

The **decoder** is a second LSTM, initialized from the encoder's final state, that takes a *proposed* future treatment sequence ā(t, t+τ-1) and rolls out Ŷ_{t+1}, ..., Ŷ_{t+τ}. The same adversarial balancing is applied at each decoder step. Because Φ is treatment-invariant, plugging in any counterfactual treatment sequence gives a valid prediction.

Identification rests on the standard assumptions: **consistency**, **positivity**, and **sequential ignorability** (no hidden confounders).

## Why it matters

CRN is the first paper to show that **representation balancing** — a workhorse of static causal inference (Shalit, Johansson, Yao) — can be made to work in the *longitudinal* setting via domain-adversarial training, sidestepping IPTW pathology entirely. It unifies deep representation learning with epidemiological causal inference. Almost every subsequent neural method for counterfactuals over time — Causal Transformer (Melnychuk et al. 2022), G-Net, TE-CDE — builds on CRN's "balancing in latent space" framing.

## Key results

CRN is evaluated on a PK-PD simulator of tumour growth under chemotherapy + radiotherapy (Geng et al., 2017), the same testbed as RMSN. A confounding parameter γ controls how strongly past tumour volume drives treatment choice. Across γ ∈ {1, ..., 10}, CRN achieves the lowest counterfactual-RMSE versus Linear, MSM, RNN, and RMSN, with the gap widening as γ grows — at γ = 10, CRN beats RMSN by ~17%. It also picks correct treatment and correct *timing* more often. T-SNE plots of Φ confirm treatment classes are visibly mixed — balancing actually happens.

## Vocabulary glossary

- **Time-varying confounders**: features at time t caused by past treatment and also influencing future treatment.
- **Treatment-invariant representation**: learned embedding Φ_t with P(Φ_t | A_t) the same for every treatment value.
- **Gradient reversal layer (GRL)**: identity forward, multiplies gradient by -λ backward; turns a classifier into an adversary.
- **IPTW**: re-weights each sample by 1 / P(observed treatment | history) to remove confounding bias.
- **Marginal Structural Models (MSMs)**: regressions fit on the IPTW-reweighted pseudo-population.
- **Recurrent Marginal Structural Networks (RMSNs)**: Lim et al. 2018; RNN-based propensity + outcome models inside the MSM framework.
- **g-computation**: alternative to IPTW; estimates the conditional outcome distribution and integrates over confounder paths.
- **Sequential ignorability**: given history, treatment is independent of future potential outcomes (no hidden confounders).
- **Positivity**: every treatment has strictly positive probability under every realizable history.
- **Potential outcomes (Neyman–Rubin)**: one outcome per possible treatment; causal estimand is a contrast over them.

## Limitations / honest take

The whole edifice rests on **sequential ignorability** — no hidden confounders at any timestep. In real EHR data this is heroic; on Reddit it is heroic squared. Treatments are assumed discrete and finite; continuous-dose needs extra machinery. Adversarial training is twitchy — the GRL coefficient λ is annealed by hand, and balancing can collapse outcome accuracy if pushed too hard. Evaluation is almost entirely on a *synthetic* tumour model with ground-truth counterfactuals; no stress-test on real EHRs. Conceptually, CRN balances a representation but does not model latent state *dynamics* the way a true world model would — Φ_t is a sufficient statistic for outcome prediction, not a causal-mechanistic state.

## Fit to my proposal

**Verdict:** main angle (counterfactual leg).

Direct mapping into my world-model-of-Reddit setup: *patient* → subreddit/community; *treatment A_t* → exogenous event or intervention regime (subreddit ban, content-policy update, viral news shock, moderation-rule change, COVID-style trigger); *outcome Y_t* → sentiment / crisis-signal trajectory I already model in my MSc; *covariates X_t* → engagement features (post volume, network density, topic mixture, user churn). The world-model latent z_t plays exactly the role of CRN's Φ_t — with a crucial upgrade: rather than a generic LSTM hidden state trained only for outcome prediction and balance, z_t is the **JEPA latent** trained jointly for (i) self-supervised next-state prediction in latent space, (ii) outcome readout, and (iii) treatment-invariance via a GRL adversary. Concretely: text/temporal encoder → z_t; predictor that rolls z_t forward conditioned on a proposed intervention sequence ā(t, t+τ-1); outcome head reading sentiment-surge probability off the rolled-out latent; adversarial head with GRL that tries to predict the intervention regime from z_t and is foiled. This gives my proposal a clean answer to *"how do you actually do counterfactuals on a world model?"* — namely, "Bica 2020's adversarial balancing, applied to the JEPA latent."

Other concrete moves: (a) cite CRN as motivation for the adversarially balanced latent, with Causal Transformer (Melnychuk 2022) as the modern self-attention successor; (b) replicate tumour-style synthetic experiments by simulating Reddit-like communities with *known* injected intervention effects, validating balancing in latent space *before* touching real data; (c) use natural experiments — r/incels ban, r/jailbait removal, r/depression policy changes, COVID lockdowns — as the only credible identification strategy on real data. Honest caveat: Reddit-scale unconfoundedness is unrealistic, so headline claims must be framed as "policy-relevant counterfactual estimates *under stated assumptions*" rather than causal truth.
