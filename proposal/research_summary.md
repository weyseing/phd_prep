# Counterfactual AI for Malaysian Time-Series — Research Summary

**Heng Wey Seing (Jeremy) — PhD enquiry, May 2026**

## The problem

Foundation-model time-series forecasters (e.g. Chronos, 2024) predict trajectories accurately but cannot reason about interventions. Counterfactual machine-learning methods (e.g. CRN, Bica et al. 2020) reason about interventions but have only been validated on small synthetic medical simulators. No method combines the two — yet *"what if?"* is exactly the question Malaysian decision-makers need:

- **Ministry of Health:** *"If we had sprayed Klang two weeks ago, how many dengue cases would we have prevented?"*
- **Tenaga Nasional:** *"Without the Hari Raya holiday, what would peak electricity demand have been?"*
- **MPOB:** *"If rainfall had been 20% higher last quarter, what palm-oil yield change should we expect?"*

## Proposed contributions

1. Extend foundation-model forecasters to accept multivariate covariates (weather, holidays, policy events).
2. Adapt adversarial-balancing counterfactual methods to operate on foundation-model latents.
3. Build the first publicly documented Malaysian benchmark for counterfactual time-series forecasting.
4. Develop an evaluation methodology for counterfactual claims when ground truth is unavailable (synthetic simulators + natural experiments + sensitivity bounds).
5. Prototype a decision-support tool for one Malaysian agency (target: MOH for dengue, or Tenaga for grid demand).

## Feasibility (Malaysian PhD compute budget)

**Compute:** laptop + Google Colab Pro + ~RM 2,000–6,000/year cloud GPU. No supercomputer required.
**Data:** publicly available (MOH iDengue, Met Malaysia, MPOB, Energy Commission). Granular intervention data via supervisor-mediated partnership where needed.
**Pace:** 4–8 year part-time, approx. 15–20 hrs/week. Each year produces a self-contained deliverable; thesis is defendable on Years 1–5 work alone if Year 6 case-study slips.

## Publication plan

Four papers across six years — Year 2 (regional/applied venue), Year 3–4 (top-tier methods), Year 4–5 (causal-ML or benchmark), Year 6 (Malaysian case study).

## Why this thesis survives 4–8 years of AI evolution

The proposal commits to **paradigms** ("foundation-model + adversarial causal balancing"), not to specific 2026 architectures. Annual literature refresh is built into Year 4. The architecture diagram is component-level; the encoder is swappable. The Malaysian dataset moat protects against scooping by Western labs.

## Supervisor profile sought

A supervisor whose work touches one or more of: time-series machine learning, causal/counterfactual inference, applied health-AI or energy-AI. Combination preferred but co-supervision across departments is welcome.

## Candidate background

Just completed: MSc Data Science, Sunway University, April 2026. Dissertation: time-series forecasting of mental-health sentiment surges on Reddit (supervisor: Prof. Dr Selina Low Yeh Ching). Full CV attached.

---

*Contact: hengweyseing53@gmail.com · LinkedIn: jeremy-heng-98b95a228 · GitHub: weyseing*
