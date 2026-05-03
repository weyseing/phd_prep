# Field 5: LLM Agents / RAG / Tool-Use
**Paper:** ReAct: Synergizing Reasoning and Acting in Language Models (2023 — Yao et al.) — https://arxiv.org/abs/2210.03629

## TL;DR

ReAct is a prompting pattern that interleaves three things in a single LLM trajectory: free-form Thought (reasoning in natural language), Action (a discrete call to a tool or environment), and Observation (the tool's response). The model alternates them until it emits a Finish action. This single, almost laughably simple template — Thought, Act, Obs, repeat — became the architectural backbone of essentially every "agent" framework that came after it (LangChain agents, AutoGPT, AgentGPT, CrewAI, OpenAI's function-calling loops, Anthropic's tool-use loops). The paper's contribution is less algorithmic than conceptual: it argued that reasoning and acting should not be separate phases but a tightly interleaved loop, where thoughts steer actions and observations correct thoughts.

## What it does (the mechanism)

The setup augments a standard action space `A` (tool calls available in the environment) with a "language action space" `L` — i.e. the model can also emit arbitrary natural-language thoughts that have no environmental effect but condition future generations. At each step the LLM samples either a thought or an action; if it's a thought, no observation comes back and the model continues; if it's an action, the environment returns an observation that is appended to the context. Few-shot prompting carries the whole load: only one or two hand-written exemplar trajectories are placed in the prompt, and the LLM (PaLM-540B in the paper, also tested with GPT-3) imitates the format.

Two domain families are tested. First, **knowledge-intensive QA** on HotpotQA (multi-hop questions) and FEVER (fact verification), where the action set is a tiny Wikipedia API: `Search[entity]` returns the first paragraph of a page, `Lookup[string]` finds a substring within the current page, and `Finish[answer]` ends the episode. Second, **interactive decision-making** in ALFWorld (a text-based household-task simulator: "heat an apple and put it in the fridge") and WebShop (a simulated e-commerce site where the agent must buy a product matching a natural-language spec). Here actions are environment commands like `go to drawer 1`, `take mug from counter`, `click[Buy Now]`.

The key contrast is against two ablations. **Act-only** (no thoughts) makes the LLM jump straight to actions, losing the ability to plan; it underperforms badly on multi-step tasks. **CoT-only** (Chain-of-Thought, reasoning without acting) lets the model reason but never check facts against the world; it hallucinates entities and gets multi-hop questions confidently wrong. ReAct sits in the middle and on QA the authors actually find the best recipe is a *hybrid*: ReAct backs off to CoT-with-self-consistency (CoT-SC) when its trajectory fails, and CoT-SC backs off to ReAct when its self-consistency vote is shaky. The two are complements, not substitutes.

## Why it matters

This paper is the moment "LLM agent" stopped being a vague aspiration and became a copy-pasteable pattern. Before ReAct, tool use in LLMs was either bolted on with brittle parsers or required RL fine-tuning (WebGPT). ReAct showed you could get state-of-the-art behaviour on tool-using tasks with prompting alone, no gradient updates, two examples in context. Every modern agent framework is a descendant: the ReAct loop is literally the default reasoning trace in LangChain, and OpenAI's and Anthropic's tool-use APIs are productised versions of the same Thought-Action-Observation cycle. It also did real work on the hallucination problem by grounding reasoning in retrieved evidence, which seeded the entire RAG-with-reasoning subfield. And because trajectories are human-readable, it cracked open interpretability and human-in-the-loop control of agents.

## Key results

On **HotpotQA**, ReAct alone scores below CoT-SC (27.4 vs 33.4 EM), but the hybrid ReAct→CoT-SC reaches 35.1, beating both. On **FEVER**, ReAct beats CoT (60.9 vs 56.3) — fact verification rewards grounding more than free-form reasoning. On **ALFWorld**, ReAct hits 71% success against the strongest imitation/RL baseline BUTLER at 37%, a ~30-point absolute jump with two prompts versus 10⁵ training trajectories. On **WebShop**, ReAct beats an RL-trained baseline by ~10 percentage points on success rate. A small but striking finding: when humans manually edit a single failed thought mid-trajectory, the agent often recovers — evidence that reasoning is causally driving behaviour, not decorative.

## Vocabulary glossary

- **Thought-Action-Observation loop** — the core ReAct cycle: reason, act, observe, repeat.
- **Action space augmentation (A ∪ L)** — extending environment actions with free-form language "actions" (thoughts) that update internal state only.
- **Grounded reasoning** — chain-of-thought conditioned on observations from a real environment, as opposed to ungrounded CoT.
- **Hallucination** — LLM confidently asserting facts it cannot verify; ReAct mitigates by replacing assertion with retrieval.
- **Chain-of-Thought (CoT)** — Wei et al.'s reasoning-only prompting baseline.
- **Self-consistency (CoT-SC)** — sampling many CoT trajectories and majority-voting the answer.
- **Few-shot in-context learning** — teaching the format with 1–6 demos in the prompt, no fine-tuning.
- **Tool use** — exposing external capabilities (search, calculator, code, APIs) to the LLM as callable actions.
- **ALFWorld / WebShop** — text-based embodied / shopping simulators used as agent benchmarks.
- **Interpretable trajectory** — the trace of Thoughts and Actions is human-readable, auditable, and editable.

## Limitations / honest take

ReAct is prompt engineering, not learning. Performance is brittle to action-space design and exemplar choice; swap a verb in a demo and accuracy drops. Long trajectories blow the context window — the paper is already close to limits on hard HotpotQA chains. The agent can lock into thought-action loops (search same entity twice, restate the same plan) with no built-in escape. The action set must be finite, named, and known in advance, which is fine for Wikipedia but awkward for open-world tasks. And because nothing is updated during use, every episode starts from zero — successors (Reflexion, FireAct, the fine-tuned-agents line) address this by adding memory or SFT on ReAct traces.

## Fit to my proposal

**Verdict:** supporting angle (interpretability / probing / stakeholder layer).

Honestly: ReAct is orthogonal to the JEPA-style world model I want to build. ReAct does prompt-level orchestration on a frozen LLM; my proposal is about *learning* latent dynamics of online communities from data and using those latents for forecasting and counterfactual analysis. ReAct has no learned dynamics, no encoder, no notion of latent state — it is not the science of the thesis.

But it is the right tool for the *deployment surface*. Three concrete uses. (1) **Counterfactual narrator.** Once the world model has produced a latent trajectory under intervention ("if megathread X had not been pinned, the predicted suicide-related posting rate falls by Y"), a ReAct agent can query a retrieval index of real Reddit posts and render the result in clinician-readable language with cited examples. (2) **World-model interrogation.** Wrap the world model in tools — `predict_sentiment_surge(community, t)`, `simulate_intervention(policy)`, `retrieve_similar_history(query)` — and let stakeholders ask questions in natural language; the ReAct loop handles the planning. (3) **Auditing.** Interpretable Thought-Action-Observation traces make moderator-facing decisions defensible and reviewable, which matters enormously in mental-health contexts.

Pitch tactic: for **applied profs** (Sunway, Taylor's, APU, UCSI, Xiamen) include a "ReAct-style stakeholder interface" subsection — they like deployable systems. For **research profs** (Monash, Nottingham), ReAct is a one-paragraph footnote on interpretability. Do not waste a chapter on it. Drop entirely if the thesis scope tightens to pure latent-dynamics theory.
