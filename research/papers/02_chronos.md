# Field 2: Time-Series Foundation Models
**Paper:** Chronos: Learning the Language of Time Series (2024 — Ansari et al.) — https://arxiv.org/abs/2403.07815

## TL;DR
Chronos tokenizes real-valued time series via mean-scaling + uniform quantization into a fixed vocabulary, then trains an off-the-shelf T5 encoder-decoder language model from scratch on ~84B observations using plain cross-entropy. The result is a probabilistic, autoregressive zero-shot forecaster that beats task-specific deep models in-domain and matches them on 27 unseen datasets — without any time-series-specific architectural changes.

## What it does (the mechanism)
Chronos reduces forecasting to next-token prediction on a discretised time series, using essentially zero domain-specific machinery. The pipeline has four stages:

**1. Mean-scaling.** Each context window is normalised by `s = (1/C) Σ |xᵢ|` (offset `m=0`). This preserves zeros (semantically meaningful) and pulls heterogeneous series into a comparable range.

**2. Uniform quantization.** Scaled values are bucketed into `B = 4094` bins with centres uniformly spaced in `[-15, +15]`. Binning is *uniform*, not quantile-based, because the value distribution at test time may differ wildly from training. Two special tokens — `PAD` and `EOS` — round out the vocabulary to 4096.

**3. Vanilla T5 training.** Tokens are fed to a stock T5 encoder-decoder. Only the input/output embedding layers are resized; attention, layer norms, training loop are unchanged. They train four T5 sizes — Mini (20M), Small (46M), Base (200M), Large (710M) — plus GPT-2 base (90M) to show decoder-only works too. Loss is plain categorical cross-entropy. No time/frequency features, lags, patches, or distribution heads are added: the model is told nothing about seasonality, or that bin `i` is closer to `i+1` than to `i+2`. Regression is recast as classification; the model discovers ordinality on its own.

**4. Probabilistic inference.** Tokens are sampled autoregressively; ~20 sample paths yield a predictive distribution; tokens are dequantized to bin centres and unscaled.

**Data pipeline.** Training uses 28 public datasets (Monash repo, M-competitions, Kaggle), totalling ~890K series and ~84B observations. They generate **10M TSMixup** augmentations — random convex combinations of `k ~ U{1,3}` real series — and **1M KernelSynth** series, drawn from Gaussian-process priors built by composing simple kernels (linear, periodic, RBF) with random `+` and `×` operators. Training mix is 9:1 augmented-real to synthetic.

## Why it matters
Chronos is the cleanest demonstration to date that **a generic LLM architecture, with zero time-series inductive bias, can be a competitive general-purpose forecaster**. Concurrent foundation models — Lag-Llama, Moirai, TimesFM, ForecastPFN — bake in domain priors (lags, patches, real-valued distribution heads, time features). Chronos throws all that out and still wins, suggesting *sequence modelling itself*, not the time-series scaffolding, is doing the work.

This launched the "treat time series as language" wave and made foundation forecasters a credible production default. The deeper methodological signal: you can pretrain a useful sequence model on **mostly synthetic** data when real data is scarce — directly relevant to mental-health discourse, where labelled crisis trajectories are rare.

## Key results
On **Benchmark I** (15 in-domain datasets), Chronos-T5 Base and Large take the top two spots on both WQL (probabilistic) and MASE (point), beating AutoARIMA, AutoETS, the SCUM statistical ensemble, and every task-specific deep model evaluated (DeepAR, PatchTST, N-BEATS, N-HiTS, TFT, DLinear, WaveNet, GPT4TS) — *and* the other pretrained foundation models (Lag-Llama, Moirai-1.0-R). Even Chronos-Mini (20M) beats Moirai-Large (311M) here.

On **Benchmark II** (27 zero-shot datasets), Chronos-Large takes 2nd on MASE, beating SCUM, all task-specific deep models, LLMTime, ForecastPFN, and Moirai. With cheap fine-tuning, Chronos-Small jumps to 1st overall. Aggregated relative WQL/MASE are reported as geometric means against Seasonal Naive (so 1.0 = baseline; lower is better) — Chronos-Large lands around 0.55-0.65 on Benchmark I.

The most striking ablation: a model trained **only on KernelSynth synthetic GP data** (no real series at all) lands close to the full pretraining run. Synthetic data alone is nearly sufficient.

## Vocabulary glossary
- **Scaling** — normalising each series so its values fit a comparable numeric range; Chronos uses mean-scaling by absolute values.
- **Quantization** — mapping continuous values to a finite set of bin indices; Chronos uses uniform binning over `[-15, +15]`.
- **Fixed vocab** — the 4094 bins + `PAD` + `EOS` form a 4096-token vocabulary the LM operates over.
- **T5 backbone** — Google's encoder-decoder transformer, used here unmodified except for vocab size.
- **Autoregressive probabilistic forecasting** — sampling future tokens one at a time; many sample paths give a predictive distribution.
- **TSMixup** — convex combinations of `k` random real series to expand training diversity.
- **KernelSynth** — GP-prior synthetic series generated from random compositions of simple kernels (`+`, `×`).
- **In-domain vs out-of-domain** — datasets seen vs unseen during pretraining; OOD == zero-shot.
- **Zero-shot** — applied to a new dataset with no fine-tuning.
- **WQL** — Weighted Quantile Loss; probabilistic-forecast metric, related to CRPS, computed over 9 quantile levels.
- **MASE** — Mean Absolute Scaled Error; point-forecast error scaled by the seasonal-naive in-sample error.

## Limitations / honest take
**Slow inference.** Autoregressive sampling over a 64-step horizon with 20 paths is dramatically slower than a single forward pass through PatchTST or DeepAR. Large is borderline impractical for high-throughput forecasting. **Univariate only** — no native cross-series or multivariate support, no obvious way to encode covariates, calendar features, or exogenous events (e.g., a policy announcement on Reddit). **Quantization artefacts.** With bin centres in `[-15s, 15s]`, sparse spike series (small `s`, occasional large value) overflow, and large-mean low-variance series lose precision (paper Fig. 16). **Trend extrapolation is weak** — exponential trends and shifting baselines confuse the model. Most importantly for me: **the latent representation is hidden inside a generative LM** — there is no clean encoder you can read off, manipulate, or use for downstream control or counterfactual rollout. It generates surface tokens; it does not give you a state.

## Fit to my proposal
**Verdict:** supporting angle.

Three concrete uses. **(1) Baseline.** Chronos is the obvious "no-learning" reference for sentiment-surge forecasting on Reddit/Twitter mental-health subs. The HuggingFace checkpoint runs zero-shot — feed in daily sentiment, post counts, distress-keyword frequencies, get a probabilistic forecast. Any latent-dynamics model I propose has to beat this in WQL/MASE, and the comparison must be in the proposal. **(2) Synthetic pretraining template.** KernelSynth proves GP-kernel synthetic series can substitute for real pretraining data. The analogue for me: synthetic discourse trajectories — composing trend, seasonality, shock-response, and recovery kernels — to pretrain my JEPA predictor when labelled crisis events are scarce. A defensible answer to "where does your data come from?" in supervisor pitches. **(3) Tokenization reference** if I ever need to discretize a latent community state for VQ-JEPA variants.

**The contrast is more important than the support.** Chronos predicts in **input space** — scalar token by scalar token, generative, surface-level. This is exactly the paradigm LeCun's JEPA argument rejects: generative models waste capacity reconstructing irrelevant detail and never form abstract state. Chronos cannot represent "the community is in a pre-crisis regime" — only "the next sentiment value is probably 2310." That is the gap my proposal claims to fill: predict in **latent space**, condition on interventions, simulate counterfactuals. Frame Chronos as the "naive baseline tier" — it forecasts the surface and ignores the dynamics. That framing motivates the whole JEPA pivot.
