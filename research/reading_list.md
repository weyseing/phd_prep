# Reading List — PhD Proposal (Jeremy)

**Constraints:** Malaysian private uni acceptance · low compute (laptop/Colab/single GPU) · future-proof for **6–8-year part-time PhD**.

**MSc thesis is decoupled.** Proposal does not need to build on or continue Reddit mental-health work. Pick the strongest topic, full stop.

---

## How to use this list

These 10 papers are **current SOTA examples** of 10 method paradigms. Specific models (Chronos, CRN, SeaLLMs) will be **obsolete by Year 4–6**. Your proposal commits to:

- a **problem** (durable, decades)
- a **method paradigm** (durable, ~10 years)
- **NOT** a specific model name (refreshed annually in lit review)

The 10 papers are scaffolding to understand the landscape. **Your contribution is the gap-bridge between them**, not adoption of any single one.

---

## Master table — re-ranked for our constraints

| Rank | # | Paper | Paradigm | Durability | Cost | What's missing (your chance to contribute) |
|---|---|---|---|---|---|---|
| 1 | 02 | Chronos (2024) | Foundation time-series forecasting | 🟢 paradigm; ⚠️ model dies fast | 🟢 Low | Handles only 1 variable at a time. Can't accept side info (weather, holidays, events). Can't answer "what if?" questions. |
| 2 | 03 | CRN (2020) | Counterfactual time-series (causal ML) | 🟢🟢 paradigm very durable | 🟢 Low | Tested only on fake tumour data. Nobody has tried it on real social/economic data, or paired with modern foundation models. |
| 3 | 07 | SeaLLMs (2023) | Multilingual SEA LLM | 🟢🟢 sovereign-AI durable | 🟡 Med | Speaks Malay/SEA languages in general, but no expert version exists for specific fields (health, law, agriculture). |
| 4 | 01 | JEPA (2022) | Self-supervised predictive learning | 🟢🟢 paradigm very durable | 🔴 from scratch / 🟢 frozen-encoder | A vision paper with diagrams, not real working examples. Nobody has applied it to a concrete real-world problem yet. |
| 5 | 05 | ReAct (2023) | LLM agents / tool-use | 🟢 durable | 🟢 Low | Just a prompting trick — the agent doesn't learn or improve from experience. |
| 6 | 06 | GraphSAGE (2017) | Inductive GNN | 🟡 paradigm durable; old paper | 🟢 Low | Works on fixed/static graphs only. Doesn't handle graphs that change over time. |
| 7 | 04 | Med-PaLM M (2023) | Multimodal generalist health AI | 🟢 durable; out-of-budget | 🔴 | Model is 562 billion parameters — too huge to run on any Malaysian PhD budget. |
| 8 | 08 | DDPM (2020) | Generative diffusion | 🟢 durable | 🔴 / 🟡 fine-tune | Built for generating pretty images. Wrong tool for forecasting or planning. |
| 9 | 10 | FourCastNet (2022) | Neural simulation surrogate | 🟢 paradigm; wrong domain | 🔴 | Only works on weather grids — not on text, social, or economic data. |
| 10 | 09 | FedAvg (2017) | Federated learning | 🟢 durable | 🟢 Low | Only useful when data must stay private. Reddit/Twitter is public, so it solves a problem we don't have. |

---

## Three durable problem-domain options (pick one)

All three survive 6–8 years.

### A. Counterfactual + foundation-model time-series — *any applied domain*

**Pitch:** "What if X hadn't happened?" reasoning layered on top of foundation forecasters. Bridges papers **02 + 03**.

**Candidate domains** (pick one, all Malaysian-friendly + low-compute):
- Dengue / influenza outbreak forecasting
- Palm-oil yield + commodity price
- Energy demand forecasting (Tenaga / SEA grid)
- Traffic congestion (KL, Penang)
- Misinformation / online-harm propagation
- Mental-health / online-discourse forecasting

**Your contributions (5 things you'd actually invent/build):**
1. **Teach foundation forecasters to accept side info** (weather, holidays, events) — current ones can't.
2. **Add "what if?" reasoning** to a foundation forecaster — current ones only predict, can't simulate interventions.
3. **Build the first Malaysian benchmark** for [dengue/energy/palm-oil] forecasting + interventions — doesn't exist today.
4. **Invent a way to check "what if?" answers** when no ground truth exists (because we can't replay history).
5. **Wrap the model into a decision tool** for the relevant Ministry / agency (MOH, Tenaga, MPOB).

**Future-proof claim:** "Counterfactual reasoning over foundation-model time-series, applied to Malaysian X."

---

### B. Multilingual / Bahasa Malaysia foundation models for a *specialised vertical*

**Pitch:** Domain-general SEA-LLMs (SeaLLMs, MaLLaM, SEA-LION) are saturating. A *vertical* (legal, health, education, agri) is wide open. Bridges papers **07 + (04 or domain)**.

**Candidate verticals:**
- Malay-language clinical / mental-health NLP
- Bahasa Malaysia legal-document reasoning
- Malay agricultural advisory chatbot
- Manglish / code-switched conversational AI

**Your contributions (5 things you'd actually invent/build):**
1. **Build the first Bahasa Malaysia [legal/health/agri] dataset** — none exists with proper expert labels.
2. **Adapt a general SEA-LLM to the specialised field** — current models fail on specialist Malay terminology (e.g. legal jargon, medical terms).
3. **Solve the Manglish problem** — handle real Malaysian text that mixes Malay + English + Chinese in one sentence.
4. **Build evaluation tests for the vertical** — can the model actually answer Malaysian-specific questions? (Cultural / legal / clinical accuracy.)
5. **Show why Western LLMs fail** on Malaysian context, and quantify what the local model fixes.

**Future-proof claim:** "Specialised Bahasa Malaysia foundation model for domain X — first of its kind."
**Funding lever:** strongest for MOE / MOSTI / MyDigital / JPA scholarships.

---

### C. Learning AI from unlabelled data — for problems where labels are scarce

**Pitch:** Train AI on raw data that has no labels. Useful when labels are too expensive or impossible to collect. Bridges papers **01 + 06 + 07**.

**Candidate problems** (pick one):
- **Predict how rare diseases progress in Malaysian patients.** Only 50–500 patients per disease — too few for normal AI. Will the patient get worse? When? Which treatment works?
- **Forecast from sparse rural sensors (Sabah/Sarawak).** Only 3–5 weather/water/air-quality sensors cover a huge area. Predict floods, air pollution, water shortage, crop stress.
- **Build AI for Malaysian languages with almost no AI support.** Iban (Sarawak, ~700K speakers), Kadazan (Sabah, ~200K speakers), Tamil-Malay code-switch (Malaysian Indians mixing Tamil + Malay in one sentence). ChatGPT and SeaLLMs barely handle these.

**Your contributions (5 things you'd actually invent/build):**
1. **Make AI learn without labels** — adapt "predict from raw data" methods to Malaysian [disease records / sensor data / dialect text].
2. **Prove unlabelled AI beats label-dependent AI** when labels are scarce — show it on real Malaysian data.
3. **Build the first dataset** for [rare-disease / rural sensors / Iban dialect] — doesn't exist today.
4. **Invent a way to test** what the no-label AI has actually learned (current testing is weak).
5. **Explain *why* it works** when data is scarce — theory paper that research-track profs love.

**Future-proof claim:** "Self-supervised predictive models for low-resource Malaysian X."
**Risk:** more research-track, harder applied-prof sell.
