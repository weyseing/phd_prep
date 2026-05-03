# Field 7: Low-resource Malaysian / SEA NLP
**Paper:** SeaLLMs — Large Language Models for Southeast Asia (2023 — Nguyen et al.) — https://arxiv.org/abs/2312.00738

## TL;DR

SeaLLMs is a recipe — not a from-scratch model — for turning an English-heavy open base model (Llama-2 in v1, Mistral and Gemma in v2/v2.5) into a competent Southeast-Asian multilingual LLM at a fraction of full-pretraining cost. The trick is four-fold: expand the tokenizer with NLLB tokens to compress non-Latin SEA scripts, continue pre-training on a curated English+SEA mix, interleave raw text with instruction data so instruction-following survives, then align via self-preferencing DPO instead of paying for human SEA-language annotators. The result beats ChatGPT-3.5 on most non-Latin SEA languages on Sea-bench, and runs much cheaper because Thai/Khmer/Burmese now tokenize at near-English rates. For my PhD pitch, this is the single highest-leverage paper for selling a Malaysian-flavoured world-model thesis to gov-funded supervisors.

## What it does (the mechanism)

The pipeline is best read as a four-stage assembly line on top of an existing English-strong base model.

**Stage 1 — vocabulary expansion.** Llama-2's tokenizer treats Thai, Khmer, Lao, and Burmese as long byte sequences — sometimes 4 to 9 tokens per character. SeaLLMs merges in tokens from Meta's NLLB tokenizer (which was trained on 200 languages including the SEA scripts) so that the SEA-language tokens-per-character ratio collapses to roughly English parity. This is not just a quality win — it is a hard inference-cost win. Every downstream user of the model pays less per Thai or Khmer query, forever.

**Stage 2 — continued pre-training.** They run causal-LM training on a filtered slice of CulturaX, mixing English with the 10 target SEA languages: Vietnamese, Indonesian, Thai, Khmer, Lao, Malay, Burmese, Tagalog/Filipino, plus English and Chinese. The mixture is weighted to avoid forgetting English while genuinely adding SEA capacity.

**Stage 3 — hybrid pre-train + SFT.** Naively continuing pre-training on raw web text destroys the instruction-following behaviour the base model picked up from RLHF. SeaLLMs interleaves raw multilingual text with instruction-format data during the same training run, which preserves chat behaviour while still ingesting new linguistic distribution. After this phase, a more standard multilingual supervised fine-tune cleans up task-following on translation, QA, math, and reasoning.

**Stage 4 — self-preferencing DPO.** This is the cleverest move. Human preference data in low-resource SEA languages is expensive and scarce. So they have the model itself generate multiple SEA-language candidates, then use the English-strong base (which is a stronger reasoner than its SEA-language self) to score and rank them. The resulting preference pairs feed Direct Preference Optimization. No SEA-language human raters needed — alignment is bootstrapped from the model's own asymmetric competence.

Coverage spans 10 SEA languages plus English and Chinese, released at 7B and 13B scales.

## Why it matters

SeaLLMs is the demonstration that regional LLMs do not require regional pretraining budgets. A modest GPU cluster, an open base, and the right post-training recipe is enough to ship something that beats GPT-3.5 on local languages. The tokenizer-merge trick alone solves a real economic problem — non-Latin SEA scripts were previously a cost tax on every API call. Self-preferencing DPO is a quietly important contribution: it shows that for any low-resource language with a strong English-base teacher, you can skip the human-annotator bottleneck entirely. And Sea-bench, while limited, is the first attempt at a region-specific evaluation that includes locale-aware safety and cultural questions, not just translated MMLU. For Malaysian, Indonesian, Vietnamese researchers this is a working blueprint — and a political artefact, since it shows SEA-led AI work can hold its own without simply rebadging Western models.

## Key results

On Sea-bench, SeaLLM-13B-Chat beats ChatGPT-3.5 on most non-Latin SEA languages across math, reasoning, safety, and locale-knowledge axes — strongest gaps on Thai, Khmer, Burmese, where GPT-3.5's English-centric tokenizer hurt it most. Tokenizer compression ratios drop dramatically for Vietnamese, Thai, Khmer, and Burmese — often 2x to 4x fewer tokens per character versus vanilla Llama-2. SeaLLM-13B is competitive with substantially larger English-only models on Malay/Indonesian QA. English performance is largely preserved, which matters because forgetting was the obvious risk of the recipe.

## Vocabulary glossary

- **Continued pre-training** — extending a base LM's pre-training on new data without resetting weights.
- **Vocabulary expansion** — adding new tokens (and embeddings) to a tokenizer post-hoc.
- **NLLB tokenizer merge** — importing tokens from Meta's No-Language-Left-Behind multilingual tokenizer.
- **Tokenizer compression ratio** — tokens-per-character; lower is cheaper.
- **Hybrid pre-train + SFT** — interleaving raw-text and instruction-format batches in one training run.
- **Multilingual SFT** — supervised fine-tuning on instruction data across multiple languages.
- **Self-preferencing DPO** — using a stronger version of the model itself to label preference pairs for DPO.
- **Sea-bench** — SEA-region evaluation suite covering reasoning, math, safety, locale knowledge.
- **Low-resource multilinguality** — modelling languages with scarce labelled data.
- **Locale-specific safety alignment** — refusal/cultural-norm tuning per region, not a global English-default policy.

## Limitations / honest take

The model remains derivative on an English/Western base — Western priors leak through, and Sea-bench is too narrow to detect subtle cultural drift. Bahasa Malaysia coverage is real but lower-priority than Vietnamese and Indonesian, where the data and team incentives are stronger. SeaLLMs is not a Malaysian model; it is a Singapore/Vietnam-led DAMO Academy / AISG-aligned effort, and its Malaysia-specific cultural and idiomatic knowledge is shallow. Newer alternatives — SEA-LION (AISG), MaLLaM (Mesolitica), Cendol (Indonesian-led), and SeaLLMs v3 itself — may now be stronger picks for Malay-only work. The self-DPO trick also has a known failure mode: any bias in the English teacher gets baked into SEA outputs.

## Fit to my proposal

**Verdict:** supporting angle (Malaysian-prof pitch lever).

Concrete use: SeaLLMs (or a competitor like AISG's SEA-LION, or Mesolitica's MaLLaM) provides a ready-made Bahasa-Malaysia-capable encoder to embed Malay-language Reddit/Twitter mental-health posts into the JEPA latent state. Without this, my proposal is English-only — fine for technical merit, but politically weak for Malaysian funding bodies (MOE, MOSTI, MyDigital, JPA scholarships).

Concrete proposal moves: **(a)** Cite SeaLLMs as the encoder choice for Malay and code-switched (Manglish) posts in the data and methods chapter. **(b)** Add a Year-3 deliverable: "Malaysian mental-health discourse" using Malay Reddit (r/Malaysia, r/Bolehland), Bahasa Twitter, and code-switched data, with the same JEPA latent-dynamics machinery applied to a Malay-language community state. **(c)** Pitch this loudly to Malaysian-flagship-funding profs at UCSI, APU, Xiamen, Sunway, and Taylor's — many of them have explicit local-language remit from their grant lines.

**Strategic note.** This single paper is the highest-leverage Malaysian-prof pitch in the entire reading list. Even if SeaLLMs is not the eventual encoder by the time I'm coding (could be MaLLaM, Cendol, or SEA-LION v3), the framing — "multilingual world model of local mental-health discourse" — is the easiest sell to gov-funded supervisors. Recommendation: write a 1-page Malay extension chapter into the proposal regardless of whether I actively pursue it. It is free pitch upside and signals national relevance without changing the technical core.

**Honest caveat.** Do not let the Malay arm become the proposal core. Annotated Malay mental-health data is scarce, the methodology is unchanged from the English version, and the contribution dilutes if I split focus. Frame it as a localization extension and a final-year chapter — not the headline.
