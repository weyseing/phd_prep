# Field 8: Generative AI / Diffusion
**Paper:** Denoising Diffusion Probabilistic Models (DDPM) (2020 — Ho, Jain, Abbeel) — https://arxiv.org/abs/2006.11239

## TL;DR

DDPM is the paper that ended the GAN era of image generation. It trains a neural network to reverse a fixed Gaussian noising process: corrupt a clean image with noise over many steps until it's pure static, then learn to undo that corruption one step at a time. The training objective collapses to a simple MSE loss — predict the noise added at step t. Sampling starts from Gaussian noise and iteratively denoises it into a coherent image. With this minimal recipe, DDPM matched or beat StyleGAN/BigGAN on CIFAR-10 without adversarial training, mode collapse, or hyperparameter brittleness. It is the methodological seed of the modern generative-image stack — DALL-E 2, Imagen, Stable Diffusion, Sora — and of diffusion in audio, 3D, video, and proteins.

## What it does (the mechanism)

There are two processes, one fixed and one learned.

**Forward process (fixed, no learning).** Given a clean image x_0, define a Markov chain that adds Gaussian noise at each of T steps (T = 1000). The variance schedule β_1...β_T is hand-chosen (linear from 1e-4 to 0.02). Algebraic trick: because each step is linear-Gaussian, you can jump from x_0 to any x_t in closed form, q(x_t | x_0) = N(√ᾱ_t · x_0, (1-ᾱ_t) I), where ᾱ_t is a cumulative product of (1 - β_s). At t = T the signal is essentially N(0, I). No network involved.

**Reverse process (learned).** The goal is to learn p_θ(x_{t-1} | x_t), the denoising kernel. Ho et al.'s key parameterization choice: instead of predicting the previous mean directly, train a network ε_θ(x_t, t) to predict the noise that was added when producing x_t from x_0. This gives the loss L_simple = E[ ‖ε - ε_θ(x_t, t)‖² ] — plain MSE between true noise and predicted noise. This objective is a reweighted, simplified form of the variational lower bound on log p(x_0); the reweighting (dropping the per-step variance terms) is what makes training stable and is the paper's biggest empirical contribution.

**Sampling.** Start with x_T ~ N(0, I). For t = T down to 1, compute x_{t-1} = (1/√α_t)(x_t - β_t/√(1-ᾱ_t) · ε_θ(x_t, t)) + σ_t z, where z ~ N(0, I). After T steps you have a sample.

**Architecture.** A U-Net backbone (PixelCNN++/Wide ResNet style) with sinusoidal embeddings of t injected at each block, plus self-attention at the 16×16 resolution. The same network handles all timesteps via the t embedding.

**Connection.** Ho et al. show ε-prediction is equivalent (up to weighting) to denoising score matching à la Song & Ermon — ε_θ approximates the score ∇ log p(x_t) — so DDPM sampling is essentially annealed Langevin dynamics. This unification is part of the paper's importance: the score-matching and likelihood-based diffusion threads merge here.

## Why it matters

Until 2020, GANs (StyleGAN2, BigGAN) ruled image generation but were notoriously unstable, prone to mode collapse, and required adversarial-loss alchemy. Likelihood-based models (VAE, normalizing flows, autoregressive) were stable but blurry or slow. DDPM was the first method to match GAN sample quality with a single, stable, single-loss training procedure that converges reliably on standard hardware. That practical recipe seeded the entire diffusion era: ADM (Dhariwal & Nichol's "Diffusion Models Beat GANs"), GLIDE, DALL-E 2, Imagen, Stable Diffusion, Sora — plus diffusion for audio (DiffWave, AudioLDM), 3D (DreamFusion), video, molecules, and proteins. It also legitimized score matching as a mainstream tool and supplied the vocabulary (forward/reverse process, ε-prediction, schedules) that every subsequent paper assumes.

## Key results

CIFAR-10: FID 3.17 unconditional — SOTA at publication, beating BigGAN and StyleGAN2. Inception Score 9.46. CelebA-HQ 256×256 and LSUN bedrooms/churches: high-fidelity unconditional samples at a quality previously only seen from adversarial models. The paper also introduces a "progressive lossy decompression" interpretation: the high-noise end of the chain encodes coarse global structure (object identity, layout), the low-noise end encodes fine detail (texture, edges) — a cleanly interpretable spectrum of what each portion of the chain learns.

## Vocabulary glossary

- **Forward / diffusion process**: fixed Gaussian noising chain x_0 → x_T.
- **Reverse / denoising process**: learned chain x_T → x_0.
- **Variance schedule β_t**: per-step noise variance (linear, cosine, etc.).
- **ε-prediction parameterization**: network outputs the noise, not the clean image.
- **Variational lower bound (ELBO/VLB)**: original training objective; L_simple drops the per-step weights.
- **L_simple**: the MSE objective Ho et al. actually train with.
- **Denoising score matching**: equivalent view — ε_θ ≈ ∇ log p(x_t).
- **Langevin dynamics**: SDE-based sampling using the score; reverse-process sampling is its discrete analogue.
- **U-Net backbone**: image-specific architecture with skip connections; standard for DDPMs.
- **Classifier-free guidance** (later, Ho & Salimans): how text/class conditioning is plugged in.
- **DDIM** (later, Song et al.): deterministic, faster sampler — same training, fewer steps.

## Limitations / honest take

The big one: sampling is slow. T = 1000 forward passes per image was painful in 2020 and is the bottleneck DDIM, DPM-Solver, and consistency models exist to fix. The model also operates in pixel space, which is wasteful — Stable Diffusion's "latent diffusion" (Rombach et al., 2022) showed you can do diffusion in a VAE latent and cut compute by an order of magnitude. There is no conditioning in this paper at all; text-to-image, class conditioning, and inpainting were bolted on by follow-ups (classifier guidance, classifier-free guidance, ControlNet). The U-Net is image-specific — for sequences, graphs, or sets the architecture has to be redesigned. Training compute is non-trivial too (CIFAR took days on TPUs). And likelihood numbers, while respectable, were not SOTA — DDPMs are sample-quality models, not density estimators, despite the variational framing.

## Fit to my proposal

**Verdict: supporting angle (contrast / vocabulary). Do not build on it.**

Honest first: DDPM is pixel-space generative modeling — the exact paradigm LeCun's JEPA position paper (paper #1) explicitly rejects. Reconstructing every pixel/token forces the model to spend capacity on perceptually noisy detail that has nothing to do with the underlying dynamics of a Reddit community. The proposal commits to JEPA-style *latent* prediction, so DDPM is not a foundation I build on.

Why include it anyway. **(1) Contrasting baseline framing.** The proposal can position itself crisply: "non-generative latent prediction (JEPA) instead of generative pixel/token reconstruction (DDPM, Chronos)." That architectural contrast makes the JEPA choice defensible to reviewers and supervisors who default to "why not just generate?". **(2) Vocabulary.** Diffusion language — forward/reverse process, score matching, ε-prediction, noise schedules — is field-standard now; I need to read and discuss it fluently. **(3) Possible future use.** If a thesis chapter wants to *generate* counterfactual discourse trajectories (synthetic Reddit threads under hypothetical interventions), latent diffusion over the JEPA world-model state space is a real research direction (e.g. DDM-2-style, latent diffusion variants). Diffusion in JEPA latent space sidesteps the pixel-space objection. **(4) Multimodal extension.** If Reddit images get pulled in (per the Med-PaLM M discussion), Stable-Diffusion-style latent diffusion is the standard image fusion path.

**Recommendation:** cite DDPM in the related-work section as the foil for non-generative SSL; do not build any model on top of it. Drop entirely if the proposal stays text-only and doesn't generate counterfactual posts. Low priority for now.
