#llm #inference #quantisation #decoding

# LLM Inference and Optimisation

## 1. Autoregressive Generation

LLMs generate text one token at a time. Each forward pass produces a probability distribution over the vocabulary, a token is sampled, and it's appended to the input for the next forward pass.

Two phases:
1. **Prefill**: process the entire input prompt in parallel (like a standard forward pass)
2. **Decode**: generate tokens one at a time, each step attending to all previous tokens

The decode phase is the bottleneck — it's sequential and memory-bound.

## 2. KV-Cache

The most important inference optimisation. During generation, the attention mechanism recomputes Keys and Values for all previous tokens at every step. The KV-cache stores these to avoid redundant computation.

### How It Works
- At each generation step, only compute Q, K, V for the **new token**
- Retrieve cached K, V for all previous tokens
- Append the new K, V to the cache
- Compute attention using the new Q against all cached K, V

### Memory Impact
For a model with $L$ layers, $H$ heads, $d_{head}$ head dimension, and sequence length $n$:

$$
\text{KV-cache size} = 2 \times L \times H \times d_{head} \times n \times \text{bytes per param}
$$

- The factor 2 is for K and V
- For LLaMA-2-70B (80 layers, 64 heads, $d_{head}$=128) at sequence length 4096 in FP16: ~40 GB just for KV-cache
- KV-cache grows linearly with sequence length — this is why long contexts are expensive

### Reducing KV-Cache Size
- **Multi-Query Attention (MQA)**: all heads share one K, V → reduces cache by $H\times$
- **Grouped-Query Attention (GQA)**: groups of heads share K, V → compromise
- **Quantised KV-cache**: store cache in INT8/INT4 instead of FP16
- **Sliding window attention**: only cache the last $W$ tokens (used by Mistral)

> **Sources**: Shazeer, "Fast Transformer Decoding: One Write-Head is All You Need" (MQA, 2019); Ainslie et al., "GQA: Training Generalized Multi-Query Transformer Models from Multi-Head Checkpoints" (2023)

## 3. Decoding Strategies

Given the logits (raw scores) from the model, how to select the next token:

### 3.1 Greedy Decoding
Always pick the token with the highest probability:

$$
x_t = \arg\max_{x} P(x | x_{<t})
$$

- Fast, deterministic
- Can produce repetitive, dull text
- Misses globally optimal sequences (a lower-probability token now might lead to a better overall sequence)

### 3.2 Beam Search
Maintain $k$ candidate sequences (beams) at each step, expanding each and keeping the top $k$:
- Explores $k$ paths simultaneously
- Finds higher-probability sequences than greedy
- Still tends toward generic/repetitive outputs for open-ended generation
- Best for: translation, summarisation (where there's a "correct" answer)

### 3.3 Temperature Scaling
Scale logits before softmax to control randomness:

$$
P(x_i) = \frac{\exp(z_i / T)}{\sum_j \exp(z_j / T)}
$$

| Temperature $T$ | Effect |
|:---------------:|:------:|
| $T < 1$ | Sharper distribution, more deterministic (model becomes more "confident") |
| $T = 1$ | Original distribution |
| $T > 1$ | Flatter distribution, more random/creative |
| $T \to 0$ | Equivalent to greedy decoding |

### 3.4 Top-k Sampling
Only sample from the $k$ most probable tokens, redistributing probability mass:
- $k = 1$: greedy decoding
- $k = 50$: common default
- Problem: fixed $k$ doesn't adapt — for peaked distributions, even top-50 includes garbage tokens; for flat distributions, top-50 may exclude valid options

> **Source**: Fan et al., "Hierarchical Neural Story Generation" (2018)

### 3.5 Top-p (Nucleus) Sampling
Sample from the smallest set of tokens whose cumulative probability exceeds $p$:
1. Sort tokens by probability (descending)
2. Include tokens until cumulative probability $\geq p$
3. Redistribute probability mass and sample

- Adaptive: includes few tokens when the model is confident, many when uncertain
- $p = 0.9$ or $p = 0.95$ are common defaults
- Generally preferred over top-k for its adaptiveness

> **Source**: Holtzman et al., "The Curious Case of Neural Text Degeneration" (2020)

### 3.6 Repetition Penalty
Penalise tokens that have already appeared to reduce repetition:

$$
z_i' = \begin{cases} z_i / \alpha & \text{if token } i \text{ has appeared} \\ z_i & \text{otherwise} \end{cases}
$$

Where $\alpha > 1$ reduces the probability of repeated tokens.

## 4. Quantisation

Reduce model size and speed up inference by using lower-precision number representations.

### Precision Formats

| Format | Bits | Range | Use Case |
|:------:|:----:|:-----:|:--------:|
| FP32 | 32 | Full precision | Training (traditional) |
| FP16 / BF16 | 16 | Reduced precision | Training + inference |
| INT8 | 8 | Integer only | Inference |
| INT4 | 4 | Very limited | Inference (aggressive) |

### Post-Training Quantisation (PTQ)
Quantise a pre-trained model without retraining:

**GPTQ** (Frantar et al., 2022):
- Layer-wise quantisation using approximate second-order information (Hessian)
- Quantises weights to INT4/INT3 with minimal accuracy loss
- One-shot: requires a small calibration dataset (~128 samples)
- Widely used for INT4 weight-only quantisation

**AWQ** (Lin et al., 2023):
- Observes that only ~1% of weights are critical (they correspond to salient activation channels)
- Protects salient weights by scaling them up before quantisation
- Often slightly better quality than GPTQ at the same bit width

**GGUF** (llama.cpp format):
- CPU-friendly quantisation format
- Supports various quantisation levels (Q4_0, Q4_K_M, Q5_K_M, Q8_0, etc.)
- Widely used for local/edge deployment

### Quantisation-Aware Training (QAT)
- Simulate quantisation during training so the model learns to be robust to it
- Better accuracy than PTQ but requires retraining
- Used when PTQ quality is insufficient

### Key Trade-offs

| Method | Size reduction | Speed gain | Quality loss | Needs retraining |
|:------:|:--------------:|:----------:|:------------:|:-----------------:|
| FP16 → INT8 | 2× | 1.5-2× | Minimal | No |
| FP16 → INT4 (GPTQ/AWQ) | 4× | 2-3× | Small | No |
| QAT INT4 | 4× | 2-3× | Very small | Yes |

> **Sources**: Frantar et al., "GPTQ: Accurate Post-Training Quantization for Generative Pre-trained Transformers" (2022); Lin et al., "AWQ: Activation-aware Weight Quantization for LLM Compression and Acceleration" (2023)

## 5. Batching Strategies

### Static Batching
- Group multiple requests into a fixed batch
- All sequences padded to the length of the longest one
- Wastes compute on padding tokens

### Continuous Batching (Orca)
- Dynamically insert and remove sequences from the batch as they finish
- No padding waste
- Much higher throughput than static batching
- Used by vLLM, TGI, and most production serving frameworks

> **Source**: Yu et al., "Orca: A Distributed Serving System for Transformer-Based Generative Models" (2022)

### PagedAttention (vLLM)
- Manages KV-cache memory like virtual memory pages in an OS
- KV-cache blocks are allocated non-contiguously, eliminating memory fragmentation
- Enables near-optimal memory utilisation and higher batch sizes
- 2-4× throughput improvement over naive KV-cache management

> **Source**: Kwon et al., "Efficient Memory Management for Large Language Model Serving with PagedAttention" (vLLM, 2023)

## 6. Speculative Decoding

Use a small, fast "draft" model to propose multiple tokens, then verify them in parallel with the large model:
1. Draft model generates $k$ candidate tokens autoregressively (fast)
2. Target model scores all $k$ tokens in a single forward pass (parallel)
3. Accept tokens that the target model agrees with; reject and resample from the point of disagreement

- Produces **exactly the same distribution** as the target model (no quality loss)
- Speedup depends on the acceptance rate (~2-3× typical)

> **Source**: Leviathan et al., "Fast Inference from Transformers via Speculative Decoding" (2023)

## 7. Common Interview Questions

- **Q: What is the KV-cache and why is it important?** It stores the Key and Value tensors from previous tokens so they don't need to be recomputed at each generation step. Without it, generation would be $O(n^2)$ in sequence length for each new token. With it, each step is $O(n)$.

- **Q: When would you use greedy vs sampling?** Greedy/beam search for tasks with a "correct" answer (translation, factual QA). Sampling (top-p + temperature) for creative/open-ended tasks where diversity matters. Temperature controls the randomness.

- **Q: How does INT4 quantisation work without destroying quality?** Methods like GPTQ use calibration data to find quantisation parameters that minimise the output error (not just the weight error). AWQ further observes that protecting the ~1% of weights corresponding to salient activations preserves most of the model's capability. The key insight is that not all weights are equally important.

- **Q: What is continuous batching?** Instead of waiting for a full batch and processing all sequences together, continuous batching dynamically adds new requests to the batch as slots free up. This eliminates wasted compute from padding and dramatically improves throughput in serving scenarios.
