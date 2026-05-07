# PhD Research Proposal — Heng Wey Seing (Jeremy)

> **Status:** DRAFT v0 — scaffolded against Sunway FET PGR application checklist (a-iii). Replace each section with full content and re-render to PDF before submission.
>
> **Sunway's required sections** (from `applications/sunway/0) Checklist for SET Postgraduate Programme Application.pdf`, item a-iii):
> 1. Title
> 2. Abstract
> 3. Methodology
> 4. Timelines (action plan + expected completion)
> 5. References
>
> **Evaluator rubric** (from item `avi`): Knowledge of field of study · Methodology suitability/currency · Writing quality.

---

## 1. Title

**Counterfactual Reasoning over Foundation-Model Time-Series — A Methodological Framework with Malaysian Public-Health and Infrastructure Case Studies**

*(Working title. Final title to be agreed with main supervisor.)*

---

## 2. Abstract

Foundation-model time-series forecasters such as Chronos [3], Lag-Llama [4] and Moirai [5] now deliver strong zero-shot predictive performance on diverse time series, but they offer no principled mechanism for **intervention reasoning** — they cannot answer the *"what if?"* questions that matter to public-sector decision-makers. Conversely, counterfactual time-series methods such as CRN [1] and Causal Transformer [2] do reason about interventions, but they have only been validated on small recurrent architectures and synthetic medical simulators. **No method bridges the two paradigms.**

This thesis proposes to close that gap. The methodological contribution is to adapt adversarial latent-balancing counterfactual methods to operate over **pretrained foundation-model latents**, preserving treatment-invariance across tokenized representations and extending the encoder to accept multivariate exogenous covariates (weather, holidays, policy events). The empirical contribution is the **first publicly documented Malaysian benchmark** for counterfactual time-series forecasting, built on MOH iDengue (spraying interventions), Energy Commission consumption data (festival/holiday natural experiments), and at least one auxiliary domain (palm-oil yield via MPOB). A layered evaluation methodology — synthetic simulators with known ground truth, natural-experiment hold-outs, and Manski-style sensitivity bounds [6] for unobserved confounders — addresses the longstanding problem of validating counterfactual claims on real-world data where ground truth is unavailable. The applied contribution is a Year-4 decision-support prototype, co-designed with one Malaysian agency (target: MOH for dengue control, or TNB / Energy Commission for grid demand).

The proposal is calibrated to a **4-year Malaysian PhD compute budget** (laptop + Colab + ~RM 2,000–6,000/year cloud GPU; no supercomputer required). Deliverables are sequenced so that Years 1–3 yield a defendable thesis on methods alone if the Year-4 case study slips. The work builds directly on the candidate's MSc dissertation in time-series forecasting at Sunway University.

---

## 3. Methodology

### 3.1 Research questions
- **RQ1.** Can foundation-model TS encoders' latent representations be conditioned on treatment variables without losing zero-shot forecasting performance?
- **RQ2.** Does adversarial latent balancing (à la Bica et al. 2020) transfer from RNN-based architectures to pretrained foundation-model latents while preserving treatment-invariance?
- **RQ3.** What evaluation methodology is appropriate for counterfactual claims on real-world Malaysian datasets where ground truth is unavailable (synthetic simulators + natural experiments + sensitivity bounds)?

### 3.2 Phases
1. **Foundation-model covariate extension.** Extend a pretrained TS foundation model (Chronos / Lag-Llama / Moirai class) to accept multivariate exogenous covariates (weather, holidays, policy events). Benchmark vs. native multivariate baselines.
2. **Counterfactual head adaptation.** Port adversarial-balancing counterfactual methods (CRN, Causal Transformer) to operate on the foundation-model latent. Define treatment-invariance loss + balancing constraint at the latent level.
3. **Benchmark construction.** Build the first publicly documented Malaysian counterfactual-TS benchmark using MOH iDengue (spraying interventions), Energy Commission consumption (holiday/festival natural experiments), and at least one auxiliary dataset (palm-oil yield via MPOB or similar).
4. **Evaluation methodology.** Develop a layered evaluation: (a) synthetic simulators with known ground truth, (b) natural experiments with held-out post-intervention windows, (c) sensitivity bounds (Manski-style) for unobserved confounders.
5. **Decision-support prototype.** Year-4 case study with one Malaysian agency (target: MOH for dengue or TNB / Energy Commission for grid demand). Working prototype + user-study evaluation with domain stakeholders.

### 3.3 Stack & compute
- **Software:** Python, PyTorch, HuggingFace Transformers, statsmodels, EconML / DoWhy for causal baselines.
- **Compute:** laptop + Google Colab Pro + ~RM 2,000–6,000/year cloud GPU (well within Malaysian PhD budget; no supercomputer required).
- **Data:** publicly available (MOH iDengue, Met Malaysia, MPOB, Energy Commission). Granular intervention data via supervisor-mediated agency partnership where needed.

### 3.4 Why this is feasible in 4 years
- Each phase is a self-contained deliverable; thesis is defendable on Years 1–3 work alone if the Year-4 agency case study slips.
- Builds directly on MSc dissertation (forecasting, calendar covariates, walk-forward CV — see *Forecasting Mental Health Sentiment Surges*) — no time lost on a cold-start literature ramp.
- No video / robotics / large-scale-RL compute requirements.

---

## 4. Timelines

| Year | Phase | Deliverable | Target venue |
|---|---|---|---|
| 1 | Literature + Phase 1 (covariate extension) | Reproduce CRN baselines on foundation-model TS; submit Paper 1 (covariate-extended foundation-model TS) | Regional / applied venue (e.g. ML4H workshop, IEEE BigData) |
| 2 | Phase 2 — counterfactual head | Paper 2: adversarial latent balancing for foundation-model TS | Top-tier methods venue (NeurIPS / ICLR / ICML) |
| 3 | Phase 3 — Malaysian benchmark + Phase 4 — evaluation methodology | Paper 3: benchmark + evaluation methodology; thesis chapter draft | Causal-ML or benchmark venue (CLeaR, NeurIPS Datasets) |
| 4 | Phase 5 — decision-support prototype + thesis write-up + viva | Paper 4: Malaysian agency case study; thesis defence | Applied venue + thesis defence |

**Mode:** 4-year target (within the 3–6 year range typical for Sunway PhD by Research). **Resilience:** each phase is a self-contained deliverable; thesis is defendable on Years 1–3 work alone if the Year-4 agency case study slips. **Currency control:** annual literature refresh, with a heavier review in Year 3, absorbs drift in foundation-model architectures (Chronos / Lag-Llama / Moirai class are committed to as a *paradigm*; specific 2026 architectures are swappable encoders in the pipeline).

---

## 5. References

[1] Bica, I., Alaa, A. M., Jordon, J., & van der Schaar, M. (2020). Estimating counterfactual treatment outcomes over time through adversarially balanced representations. *International Conference on Learning Representations (ICLR).*

[2] Melnychuk, V., Frauen, D., & Feuerriegel, S. (2022). Causal Transformer for estimating counterfactual outcomes. *International Conference on Machine Learning (ICML).*

[3] Ansari, A. F., Stella, L., Turkmen, C., Zhang, X., Mercado, P., Shen, H., Shchur, O., Rangapuram, S. S., Pineda Arango, S., Kapoor, S., et al. (2024). Chronos: Learning the language of time series. *arXiv:2403.07815.*

[4] Rasul, K., Ashok, A., Williams, A. R., Khorasani, A., Adamopoulos, G., Bhagwatkar, R., Biloš, M., Ghonia, H., Hassen, N. V., Schneider, A., Garg, S., et al. (2024). Lag-Llama: Towards foundation models for probabilistic time series forecasting. *arXiv:2310.08278.*

[5] Woo, G., Liu, C., Kumar, A., Xiong, C., Savarese, S., & Sahoo, D. (2024). Unified training of universal time series forecasting transformers (Moirai). *International Conference on Machine Learning (ICML).*

[6] Manski, C. F. (1990). Nonparametric bounds on treatment effects. *American Economic Review*, 80(2), 319–323.

[7] Lim, B., Arık, S. Ö., Loeff, N., & Pfister, T. (2021). Temporal Fusion Transformers for interpretable multi-horizon time series forecasting. *International Journal of Forecasting*, 37(4), 1748–1764.

[8] Lim, B., Alaa, A., & van der Schaar, M. (2018). Forecasting treatment responses over time using recurrent marginal structural networks. *Advances in Neural Information Processing Systems (NeurIPS).*

[9] Hernán, M. A., & Robins, J. M. (2020). *Causal Inference: What If.* Boca Raton: Chapman & Hall / CRC.

[10] Pearl, J. (2009). *Causality: Models, Reasoning, and Inference* (2nd ed.). Cambridge University Press.

[11] Das, A., Kong, W., Sen, R., & Zhou, Y. (2024). A decoder-only foundation model for time-series forecasting (TimesFM). *International Conference on Machine Learning (ICML).*

[12] Salinas, D., Flunkert, V., Gasthaus, J., & Januschowski, T. (2020). DeepAR: Probabilistic forecasting with autoregressive recurrent networks. *International Journal of Forecasting*, 36(3), 1181–1191.

[13] Ministry of Health Malaysia. *iDengue Geo-Portal — National Dengue Surveillance Data.* https://idengue.mysa.gov.my (accessed May 2026).

[14] Energy Commission of Malaysia (Suruhanjaya Tenaga). *Energy data and publications.* https://www.st.gov.my (accessed May 2026).

*Annual literature refresh planned in Year 3 to capture post-2026 advances in foundation-model time-series and counterfactual ML.*
