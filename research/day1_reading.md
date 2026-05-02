# Day 1 — Future-Proof AI Field Survey

**Goal:** In one day (~6 hours), survey **10 future-proof AI fields** to identify which 2–3 pull you most. This is BREADTH-first — no deep reads tomorrow. Day 2 will go deep on your top picks.

**Constraint:** All 10 fields are framed so Malaysian professors will accept them — applied, future-proof, compute-feasible.

---

## The 10 fields surveyed today

| # | Field | Future-proof reason | Malaysian-prof acceptance |
|---|---|---|---|
| 1 | World Models / JEPA / Predictive SSL | LeCun's AGI agenda; foundation for autonomy | ✅ if framed applied |
| 2 | Time-Series Foundation Models | TimeGPT/Chronos/Moirai — explosive growth | ✅✅ very high (applied) |
| 3 | Causal AI / Counterfactual Reasoning | XAI mandates; healthcare needs causality | ✅ high |
| 4 | Multimodal AI for Health | Multi-decade healthcare AI tailwind | ✅✅✅ very high |
| 5 | LLM Agents / RAG / Tool-Use | Most active 2025–2026 research area | ✅✅ very high |
| 6 | GNN / Network Science | Social/biomedical/financial networks | ✅ high |
| 7 | Low-resource Malaysian NLP | SEA AI sovereignty trend | ✅✅✅ very high (gov-funded) |
| 8 | Generative AI / Diffusion Models | Image/video/audio generation explosion | ✅ high |
| 9 | Federated / Privacy-Preserving AI | Health/finance regulation-driven | ✅ moderate |
| 10 | AI for Climate / Palm Oil / Agriculture | Malaysian flagship area | ✅✅✅ very high (gov-funded) |

**Today's mode:** 10 papers × ~30 min skim each = ~5 hrs reading + 1 hr synthesis. **All skim-only.**

---

## Skim recipe (~30 min per paper)

For each paper, read in this order:
1. **Title + abstract** (5 min) — what problem? what method?
2. **Intro: last 1–2 paragraphs** (5 min) — claimed contribution
3. **Figures + captions** (10 min) — usually shows the method better than the text
4. **Conclusion** (5 min) — limitations + future work
5. **Skim related work headings only** (5 min) — vocabulary

Goal: be able to say in 2 sentences what the paper does, why it matters, and where it would slot in YOUR proposal landscape.

---

## Morning Block (≈3 hrs) — 6 papers

### 9:00–9:30 — Field 1: JEPA / World Models
**LeCun (2022)** — *"A Path Towards Autonomous Machine Intelligence"*
- The vision document for JEPA + world models
- Skim sections 1–3, then "JEPA" section, then conclusion
- Search: *"LeCun A Path Towards Autonomous Machine Intelligence"*

### 9:30–10:00 — Field 2: Time-Series Foundation Models
**Ansari et al. (2024)** — *"Chronos: Learning the Language of Time Series"*
- arXiv:2403.07815
- Most relevant to your MSc forecasting work

### 10:00–10:30 — Field 3: Causal AI
Search: *"counterfactual outcomes time series neural network van der Schaar"* — pick a recent (2020+) paper from her lab
- Skim: what does "counterfactual" mean here? What's the use case?

### 10:30–10:45 — ☕ break

### 10:45–11:15 — Field 4: Multimodal Health AI
Search: *"multimodal foundation model healthcare 2024 survey"* — pick one (Med-PaLM M, BiomedGPT, or a recent survey)
- Skim: which modalities fused? Architecture pattern?

### 11:15–11:45 — Field 5: LLM Agents / RAG
**Yao et al. (2023)** — *"ReAct: Synergizing Reasoning and Acting in Language Models"*
- arXiv:2210.03629
- The seminal LLM-agent paper; understand the agent loop

### 11:45–12:15 — Field 6: GNN / Network Science
Search: *"graph neural network social network 2023 2024"* — pick a recent application paper, ideally on online communities or health networks
- Skim: how does message-passing on a social graph work?

---

## Lunch (12:15–13:00)

---

## Afternoon Block (≈2.5 hrs) — 4 papers

### 13:00–13:30 — Field 7: Low-resource Malaysian NLP
Search: *"Bahasa Malaysia NLP low-resource"* OR *"Malaysian language model"* OR *"SEA-LION SEALion Singapore Southeast Asian language"*
- Skim: what's the state of NLP for BM/Malay/code-mixed text?
- This is the field where Malaysian profs have **most pride and funding**

### 13:30–14:00 — Field 8: Generative AI / Diffusion
**Ho et al. (2020)** — *"Denoising Diffusion Probabilistic Models"* (DDPM)
- arXiv:2006.11239
- Foundational paper for diffusion. Skim the loss + sampling algorithm boxes only.

### 14:00–14:30 — Field 9: Federated / Privacy AI
**McMahan et al. (2017)** — *"Communication-Efficient Learning of Deep Networks from Decentralized Data"* (FedAvg)
- arXiv:1602.05629
- Foundational federated learning paper. Skim: what problem does federated solve?

### 14:30–14:45 — ☕ break

### 14:45–15:15 — Field 10: AI for Climate / Agriculture
Search: *"deep learning palm oil"* OR *"AI agriculture Southeast Asia"* OR *"FourCastNet weather forecasting"*
- Skim: what's the ML approach? What's the Malaysian-relevant version?

### 15:15–15:30 — ☕ break

---

## End-of-Day Synthesis (15:30–16:30) — **most important hour**

Create `research/day1_synthesis.md` and answer:

### 1. Gut-feel ranking (5 min)
Rank the 10 fields from "most pull" to "least pull" — first instinct, no overthinking.

### 2. Top 3 fields and why (15 min)
Your top 3, with 2–3 sentences each on **why** they pull you. Be honest — "because it's hot" is a valid reason; "because I already know it" is also valid.

### 3. Fusion candidates (15 min)
Which 2 fields would naturally combine in a single PhD topic? List 3 candidate fusions, e.g.:
- "World Models + Time-Series Foundation Models" → discourse dynamics forecasting
- "GNN + LLM Agents" → social-network reasoning agents
- "Low-resource Malaysian NLP + Causal AI" → causal claim verification in BM
- ...

### 4. Confusions (10 min)
List 5 questions or confusions you ended the day with. We'll work through them tomorrow.

### 5. Where your MSc fits (10 min)
Of your top 3 fields, which one is the **most natural extension** of your MSc thesis (Reddit mental-health sentiment forecasting)? Even if it's not your favorite — note it. The "most natural extension" path is the easiest to convince Malaysian profs about.

### 6. Where your MSc DOESN'T fit (5 min)
Of your top 3 fields, which one would require you to **abandon** your MSc as a foundation? That's a pivot, and it's a real cost. Note which.

---

## Notes template for tomorrow

For each of the 10 papers, capture in `research/papers_read.md`:

```
## Field N: [Field name]
### [Paper title] (Year — Authors) — arXiv/link
- **What it does (1 sentence)**:
- **Why it matters (1 sentence)**:
- **Vocabulary / concepts to remember**:
- **Could fit my proposal as**: [main angle / supporting angle / not relevant]
```

Keep notes terse. Tomorrow is breadth, not depth.

---

## Tools while you read

- **Google Scholar** — primary search; sort by date for recent work
- **arXiv** — fastest way to recent papers
- **Connected Papers** (connectedpapers.com) — visualize neighborhood of a seed paper
- **Semantic Scholar** — citation chasing
- **Hugging Face Papers** — daily-curated trending AI papers (https://huggingface.co/papers)

---

## Stop criteria

You've completed Day 1 when you can answer in 2 sentences each (no notes):
1. What is each of the 10 fields about?
2. Which 3 fields pull you most, and why?
3. Which 2 fields would you fuse for a proposal?
4. Which fields would force you to abandon your MSc as a foundation?

If question 3 is fuzzy — that's fine, that's tomorrow's discussion.

---

## What this is NOT

- Not deep — no paper read deeply tomorrow
- Not exhaustive — there are still ~10 more AI fields beyond these 10 (quantum ML, neuromorphic, AI hardware, edge AI, recommender systems, knowledge graphs, AI for cybersecurity, AI for education, robotics/autonomous systems, computer vision/medical imaging). I cut these because they're either niche, well-trodden, or hard to supervise in Malaysia. Ask me if you want any added.
- Not the final list — after synthesis you'll narrow to 2 fields. Day 2 = deep reads on those 2.
