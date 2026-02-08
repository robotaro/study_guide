#llm #modeseeking #pretraining

# LLM Core Concepts

**Reference video**: https://www.youtube.com/watch?v=Bpgloy1dDn0

## 1. Tokenisation

LLMs don't operate on raw text — they process **tokens** (subword units).

### Byte-Pair Encoding (BPE)
The dominant tokenisation algorithm (used by GPT, LLaMA, Mistral):
1. Start with individual characters (or bytes) as the vocabulary
2. Count all adjacent pairs in the training corpus
3. Merge the most frequent pair into a new token
4. Repeat until the desired vocabulary size is reached

- Vocabulary sizes: GPT-2 (50,257), LLaMA (32,000), GPT-4 (~100,000)
- Handles unseen words by decomposing them into known subwords
- **SentencePiece** (Kudo & Richardson, 2018): language-agnostic tokeniser that operates on raw text (no pre-tokenisation). Used by LLaMA, T5

**Why it matters**: tokenisation affects model performance, cost (more tokens = more compute), and multilingual capability. Poor tokenisation for a language means more tokens per word, increasing cost and reducing effective context length.

> **Sources**: Sennrich et al., "Neural Machine Translation of Rare Words with Subword Units" (2016); Kudo & Richardson, "SentencePiece: A simple and language independent subword tokenizer and detokenizer for Neural Text Processing" (2018)

## 2. Pre-training (Distribution Matching)

Pre-training teaches the model to predict the next token given all preceding tokens. The model learns to **match the distribution** of its training data.

### Objective: Causal Language Modelling

$$
\mathcal{L} = -\sum_{t=1}^{T} \log P(x_t | x_1, \dots, x_{t-1}; \theta)
$$

- The model learns grammar, facts, reasoning patterns, and world knowledge from massive corpora
- Training data: typically trillions of tokens from web crawls (Common Crawl), books, code, Wikipedia
- This phase is the most expensive (millions of GPU-hours)

### Scaling Laws
Kaplan et al. (2020) and Chinchilla (Hoffmann et al., 2022) showed predictable relationships between model performance and:
- **Model size** (number of parameters $N$)
- **Dataset size** (number of tokens $D$)
- **Compute budget** (FLOPs $C$)

**Chinchilla optimal**: for a given compute budget, model size and data size should be scaled equally. This means many early models (e.g., GPT-3 175B trained on 300B tokens) were **undertrained** — Chinchilla (70B params, 1.4T tokens) matched GPT-3 performance with 4× fewer parameters.

> **Sources**: Kaplan et al., "Scaling Laws for Neural Language Models" (2020); Hoffmann et al., "Training Compute-Optimal Large Language Models" (Chinchilla, 2022)

## 3. Post-Training Alignment

After pre-training, the model can generate fluent text but isn't aligned with human preferences. Post-training narrows the output distribution from "everything the internet says" to "helpful, harmless, honest responses".

### Supervised Fine-Tuning (SFT)
- Train on curated (prompt, response) pairs written by humans
- Teaches the model the desired output format and style
- Relatively cheap compared to pre-training

### RLHF (Reinforcement Learning from Human Feedback)
1. **Reward model training**: humans rank model outputs → train a reward model to predict human preferences
2. **RL fine-tuning**: optimise the LLM policy to maximise the reward model's score, with a KL penalty to stay close to the SFT model

$$
\mathcal{L}_{RLHF} = \mathbb{E}_{x \sim \pi_\theta} [R(x)] - \beta \cdot D_{KL}(\pi_\theta || \pi_{SFT})
$$

- The KL penalty prevents reward hacking (the model exploiting quirks of the reward model)
- Used by InstructGPT, ChatGPT, Claude

### DPO (Direct Preference Optimisation)
- Eliminates the need for a separate reward model
- Directly optimises the policy on preference pairs (chosen vs rejected responses)
- Simpler, more stable training than RLHF
- The key insight: the optimal RLHF policy can be expressed in closed form, so you can train directly on preferences

$$
\mathcal{L}_{DPO} = -\mathbb{E}_{(x, y_w, y_l)} \left[ \log \sigma \left( \beta \log \frac{\pi_\theta(y_w|x)}{\pi_{ref}(y_w|x)} - \beta \log \frac{\pi_\theta(y_l|x)}{\pi_{ref}(y_l|x)} \right) \right]
$$

Where $y_w$ is the preferred response and $y_l$ is the rejected response.

> **Sources**: Ouyang et al., "Training language models to follow instructions with human feedback" (InstructGPT, 2022); Rafailov et al., "Direct Preference Optimization: Your Language Model is Secretly a Reward Model" (DPO, 2023)

## 4. Mode Seeking vs Distribution Matching

A useful mental model for understanding the training stages:

| Stage | Objective | Analogy |
|:-----:|:---------:|:-------:|
| Pre-training | Distribution matching | Learn everything the internet knows |
| RLHF/DPO | Mode seeking | Narrow down to the "best" responses; snip off undesirable trajectories |

- **Distribution matching**: model tries to cover the full distribution of training data (including toxic, wrong, unhelpful content)
- **Mode seeking**: model collapses onto the modes (peaks) of the distribution that align with human preferences

## 5. Dynamic Context Injection

LLMs are **not** knowledge bases — their knowledge is frozen at training time and can be wrong. Dynamic context injection addresses this:

- Inject relevant, up-to-date information into the prompt at inference time
- Examples: user profile data, database query results, retrieved documents (RAG)
- The model uses in-context information preferentially over parametric knowledge
- This is the foundation of production LLM applications

## 6. Prompt Templates and Chat Formats

Different models expect different input formats:

- **Chat models**: use role-based templates (system, user, assistant)
- **Instruct models**: use instruction-following format
- The template must match what the model was fine-tuned on, otherwise performance degrades

Example (ChatML format used by many models):
```
<|system|>You are a helpful assistant.<|end|>
<|user|>What is attention?<|end|>
<|assistant|>
```

## 7. Common Interview Questions

- **Q: What is the difference between pre-training and fine-tuning?** Pre-training learns general language understanding from massive unlabelled data (next-token prediction). Fine-tuning adapts the model to specific tasks or behaviours using smaller, curated datasets (SFT) or human preferences (RLHF/DPO).

- **Q: Why not just train on high-quality data from the start?** Scale matters. Pre-training on trillions of diverse tokens builds broad capabilities. High-quality data alone is too small to learn the full distribution of language. The two-stage approach (broad pre-training → targeted alignment) is more effective than either alone.

- **Q: What are scaling laws and why do they matter?** They show that model performance improves predictably with more compute, data, and parameters. This lets you forecast performance before training, plan compute budgets, and make architecture decisions. Chinchilla showed that many models were undertrained — you should scale data proportionally with model size.
