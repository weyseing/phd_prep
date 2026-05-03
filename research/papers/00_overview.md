# Day 1 — 10-Paper Overview (proposal-ideation cheat sheet)

**Generated:** 2026-05-03 — Claude wrote these summaries; Jeremy still needs to skim 3–4 actual abstracts before drafting.

**Proposal anchor:** *World Model of Online Mental-Health Communities — JEPA-style latent dynamics for forecasting + counterfactual analysis of Reddit/Twitter discourse.*

---

## Fit ranking (by Claude — react to this with gut feel)

### 🟢 MAIN ANGLE — proposal would directly cite/build on these

| # | Paper | Why it's main angle |
|---|---|---|
| **01** | **LeCun 2022 — JEPA position paper** | The architectural blueprint. H-JEPA's multi-time-scale latent dynamics maps onto post→thread→community levels; latent z naturally hosts unobserved triggers needed for counterfactuals. |
| **03** | **Bica 2020 — Counterfactual Recurrent Network** | Near drop-in mechanism for the counterfactual leg. Replace patient/treatment/outcome → community/event/sentiment. Adversarial balancing on a recurrent latent is exactly what de-biases time-varying confounders (news cycles, prior interventions). |

### 🟡 SUPPORTING ANGLE — relevant but not the spine

| # | Paper | Role in the proposal |
|---|---|---|
| 02 | Chronos (time-series FM) | Strong zero-shot baseline to beat; KernelSynth-like augmentation template; tokenization reference if I want a discrete codebook over latent community states. |
| 04 | Med-PaLM M (multimodal health) | Tangential to JEPA, but canonical reference if Reddit *image+text* fusion (memes, screenshots in r/depression) gets pulled in. |
| 05 | ReAct (LLM agents) | Probing/analysis layer — agent that queries the world model and narrates counterfactuals in human-readable form for clinicians/moderators. Not core. |
| 06 | GraphSAGE (GNN) | Foundational reading. If world-model encoder needs Reddit reply/comment graph embeddings on evolving graphs, this is the canonical inductive-GNN reference. |
| 07 | SeaLLMs (SEA NLP) | **Localization extension** — Bahasa-Malaysia encoder for Malay-language Reddit/Twitter posts. **Strongest Malaysian-prof pitch lever** without changing the thesis core. |
| 08 | DDPM (diffusion) | Contrast/vocabulary — JEPA explicitly *rejects* pixel-space generative modelling; DDPM is the foil that justifies non-generative latent prediction. Also: relevant if I ever want to *generate* counterfactual discourse trajectories. |
| 10 | FourCastNet (weather) | Methodological inspiration — different domain, but autoregressive latent rollout + "ensemble by perturbing initial conditions for counterfactuals" validates the template. Cite for legitimacy. |

### 🔴 NOT RELEVANT (unless scope shifts)

| # | Paper | Why it's out |
|---|---|---|
| 09 | FedAvg (federated) | Reddit/Twitter is public — federation isn't needed. Only relevant if proposal extends to clinical/hospital text. |

---

## Suggested gut-check ranking (Claude's guess — please push back)

1. **JEPA (01)** — non-negotiable, proposal core
2. **CRN (03)** — non-negotiable, counterfactual leg
3. **Chronos (02)** — strongest baseline, MSc continuation
4. **SeaLLMs (07)** — Malaysian-prof lever
5. **FourCastNet (10)** — methodological cousin (ensemble forecasting in latent space)
6. **GraphSAGE (06)** — only if community-graph encoding becomes load-bearing
7. **DDPM (08)** — vocabulary/contrast paper, not built upon
8. **ReAct (05)** — interpretability layer, optional
9. **Med-PaLM M (04)** — only if multimodal Reddit posts get pulled in
10. **FedAvg (09)** — drop unless scope shifts to clinical data

---

## Fusion candidates for a single PhD thesis

| Fusion | Pitch in 1 line | Best target profs |
|---|---|---|
| **JEPA + CRN** | "Latent world model of online mental-health discourse with adversarially balanced counterfactuals" | Monash, Nottingham (research-track) |
| **JEPA + Chronos** | "Foundation-model time-series backbone + JEPA latent predictor for community sentiment forecasting" | Sunway, Taylor's (applied-track) |
| **JEPA + SeaLLMs** | "Multilingual world model of Malaysian mental-health discourse (Malay + English Reddit/Twitter)" | UCSI, APU, Xiamen (Malaysia-flagship) |
| **JEPA + GraphSAGE** | "Graph-aware latent dynamics for Reddit communities — modeling discourse propagation through reply-graph topology" | Sunway, Monash |
| **CRN + ReAct** | "Counterfactual narrator — LLM agent that interrogates a learned world model to explain 'what if'" | Nottingham (interpretability angle) |

---

## Pitch-level mapping (per CLAUDE.md)

- **Applied-AI / health-AI profs (Sunway, Taylor's, APU, UCSI, Xiamen):** lead with **02 + 03** ("Chronos-style forecasting + counterfactual reasoning for mental-health discourse"). Drop "world model" jargon.
- **Research-track profs (Monash, Nottingham):** lead with **01 + 03 + 10** ("JEPA-style world model + adversarially balanced counterfactuals + ensemble intervention forecasting"). Full framing.
- **Malaysian-flagship pitch (any Malaysian prof):** layer **07** on top — "Bahasa Malaysia mental-health discourse" — funding-friendly.

---

## Open confusions for tomorrow (Day 2 deep reads)

1. JEPA loss in practice — what's the actual training signal on text/time-series? (LeCun is conceptual; need V-JEPA or I-JEPA implementation paper)
2. CRN scaling — does adversarial balancing work on a 100k-user community graph or only small clinical cohorts?
3. Time-series counterfactuals beyond CRN — is **Causal Transformer** (Melnychuk 2022) the more recent SOTA?
4. JEPA + sequences — is there a JEPA variant for irregular time-series / event streams (Reddit posts arrive irregularly)?
5. How to get unethical-to-randomize counterfactuals on real Reddit — synthetic interventions? Natural experiments (subreddit bans, policy changes)?

---

## Recommended next step (tonight)

1. **Skim the abstracts of 01, 02, 03** directly (10 min total) — confirm Claude's framing.
2. **Pick fusion:** likely **01 + 03** (research-track) or **01 + 02** (applied-track). Decide which prof tier you're optimizing for.
3. **Draft 1-page proposal sketch** in `proposal/v0_sketch.md` with: problem statement, JEPA + CRN-style architecture diagram (text), 3 use cases (forecast / counterfactual / intervention), data plan (Reddit + optionally Malay Twitter via SeaLLMs).
4. **Tomorrow (Day 2):** deep-read 01 + 03 (and probably the V-JEPA / Causal Transformer follow-ups).
