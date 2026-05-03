# Field 4: Multimodal AI for Health
**Paper:** Towards Generalist Biomedical AI (Med-PaLM M) (2023 — Tu et al.) — https://arxiv.org/abs/2307.14334

## TL;DR

Med-PaLM M is Google's attempt at a single generalist biomedical AI: finetune PaLM-E (PaLM language model + ViT vision encoder) on MultiMedBench — 14 tasks across 12 datasets spanning medical Q&A, radiology and pathology images, chest X-ray report generation, and even genomic variant calling. One set of weights, instruction-tuned, sequence-to-sequence everywhere. Headline claim: a single generalist model matches or beats per-task specialist SOTAs across heterogeneous medical modalities, exhibits emergent zero-shot reasoning on held-out tasks, and produces chest X-ray reports that radiologists prefer over real radiologist reports up to 40.5% of the time. Landmark proof-of-concept for "one model, many medical modalities" — but supervised, generative, and clinical-imaging-flavoured. Tangential to a JEPA world-model of Reddit, but useful as a vocabulary anchor and a hedge if image posts get pulled into scope.

## What it does (the mechanism)

The backbone is **PaLM-E**, originally an embodied-AI model: take PaLM (a decoder-only LLM) and a Vision Transformer (ViT), project ViT image patch embeddings into the PaLM token space, and interleave them with text tokens. The model then sees a single multimodal sequence — text, image, text, image, text — and decodes a text answer. No special heads per task; everything is text-out. This is the unified seq-to-seq generative framework the paper leans on hard.

Tu et al. then build **MultiMedBench**, a 14-task, 12-dataset, 7-modality benchmark covering: medical question answering (MedQA, MedMCQA, PubMedQA), radiology report summarization (MIMIC-III), visual question answering (Slake-VQA, Path-VQA), chest X-ray report generation (MIMIC-CXR), medical image classification (chest X-ray, dermatology, mammography, pathology patches), and — the surprising one — **genomic variant calling**, where DNA pile-ups are encoded as image-like tensors and variants are emitted as text strings. Genomics gets shoehorned into the same text-out paradigm: variant types and positions are literally generated as tokens.

Three model sizes are trained: **12B, 84B, 562B** parameters. All are instruction-tuned with task-specific natural-language prompts ("Answer the following multiple-choice question…", "Generate a chest X-ray report for this image…"), then finetuned jointly on the full MultiMedBench mixture. Crucially, this is **one set of weights** evaluated on all tasks — no per-task heads, no per-task adapters. The recipe is straightforward: assemble a heterogeneous medical mixture, reformat everything as instruction-following seq-to-seq, finetune a frozen-ish multimodal foundation model.

## Why it matters

It is the first credible demonstration that a *single generalist* model can match per-task specialists across radically different medical modalities — text, 2D imaging at multiple scales, and genomic tensors — without per-task architecture surgery. Two findings are durable: (1) **positive task transfer** — joint training on more tasks improved performance on each individual task vs single-task finetuning, contradicting the common worry that mixing heterogeneous medical data dilutes performance; and (2) **emergent zero-shot medical reasoning** — the model handled held-out tasks (chain-of-thought medical Q&A, never-seen chest X-ray pathologies) it was never finetuned on. The radiologist evaluation is the marketing-friendly headline but the transfer/emergence findings are the scientifically interesting ones, because they suggest that the "scale + instruction-tune + unify" recipe that worked for general LLMs also works for medicine.

## Key results

State-of-the-art or near-SOTA on most MultiMedBench tasks at the 562B scale, beating prior specialist models on several. On MIMIC-CXR chest X-ray report generation, side-by-side radiologist evaluation showed Med-PaLM M reports were preferred over reports written by real radiologists in up to **40.5% of pairwise comparisons** — striking, though far short of dominance. Genomic variant calling was competitive with **DeepVariant**, the field-standard specialist tool, despite genomics being ingested as text. Held-out generalization: the model classified chest X-ray pathologies it had never been trained on, and produced reasonable chain-of-thought for medical reasoning prompts not in its training mix. Scale mattered: the 562B model materially outperformed 12B/84B on the harder generative and reasoning tasks, less so on classification.

## Vocabulary glossary

- **PaLM-E** — Google's multimodal LLM that injects ViT image embeddings into the PaLM token stream.
- **ViT (Vision Transformer)** — image encoder that splits images into patches and treats them as tokens.
- **MultiMedBench** — the 14-task / 12-dataset / 7-modality biomedical benchmark introduced in this paper.
- **Instruction tuning** — finetuning a model on (instruction, response) pairs so it follows natural-language task prompts.
- **Generalist medical AI** — one model handling many medical tasks/modalities vs per-task specialists.
- **Modality fusion** — the projection trick that maps image embeddings into the same space as text tokens.
- **Side-by-side radiologist evaluation** — clinicians blindly rank model-generated vs human-written reports.
- **Positive task transfer** — joint multi-task training improving per-task performance over single-task finetuning.
- **Zero-shot medical reasoning** — solving medical tasks not seen during finetuning.
- **Genomic variant calling** — identifying SNPs/indels from DNA sequencing reads; here cast as text generation.

## Limitations / honest take

Hallucinations remain a real and unresolved problem — the model invents findings in reports. Bias evaluation is thin (mostly aggregate metrics, no demographic stress-tests). Nothing was deployed clinically; all evaluation is offline and benchmark-driven. Compute is enormous (the 562B variant is unreachable for a Malaysian PhD budget — and even the 12B is non-trivial). Modality coverage is narrower than it looks: imaging plus text plus genomics-as-text, but no audio, no time-series EHR, no longitudinal patient trajectories, no proper sequence-level genomics. Generative outputs are notoriously hard to verify clinically — the 40.5% radiologist preference is impressive but also means human reports were preferred ~60% of the time, and inter-rater agreement was modest. Datasets skew Western (MIMIC, US dermatology cohorts), so generalization to Malaysian clinical populations is unproven.

## Fit to my proposal

**Verdict:** supporting angle (only if multimodal Reddit gets pulled in).

Honest take first: Med-PaLM M is a *supervised generative* multimodal LLM aimed at clinical imaging and genomics — a fundamentally different paradigm from a JEPA-style latent-dynamics model of social discourse. It is **not directly relevant** to the world-model spine of my proposal. The objects of study (chest X-rays vs Reddit threads), the learning paradigm (supervised generative vs self-supervised predictive), and the use cases (diagnosis vs forecasting + counterfactual simulation) all diverge.

Where it could matter: (1) Reddit posts in r/depression, r/SuicideWatch, r/SelfHarm increasingly contain images — memes, screenshots of texts, selfies, self-harm photos that get auto-blurred. If I extend the proposal to multimodal community state, Med-PaLM M is the canonical reference for "how to fuse medical text + image into one model" and for the projection-into-token-space trick. (2) **Vocabulary borrow** — the "generalist biomedical AI" framing is rhetorically powerful and helps pitch to health-AI profs at Sunway, Taylor's, APU, UCSI; I can name-check it without adopting the method. (3) Methodologically, instruction-tuning a single model across heterogeneous mental-health tasks (sentiment forecasting + crisis detection + counterfactual narration via something ReAct-shaped) is a Med-PaLM M-style move, even if the encoder is JEPA-shaped underneath.

Bottom line: keep this paper in the back pocket as the multimodal-extension citation; do **not** build the proposal core around it. If I scope the PhD to text-only Reddit (which mirrors the MSc and is easier to defend on a Malaysian compute budget), Med-PaLM M drops out entirely. That is probably the right call for v1.
