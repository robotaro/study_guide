#llm #finetuning #lora #peft

# LLM Fine-Tuning Methods

## 1. Overview

Fine-tuning adapts a pre-trained LLM to a specific task or domain. The key trade-off: **more parameters updated = better adaptation but higher cost and overfitting risk**.

| Method | Params updated | GPU memory | Quality | Use case |
|:------:|:--------------:|:----------:|:-------:|:--------:|
| Full fine-tuning | All | Very high | Best (with enough data) | Large datasets, critical tasks |
| LoRA | <1% | Low | Near full fine-tuning | Most practical scenarios |
| QLoRA | <1% | Very low | Slightly below LoRA | Consumer GPUs, prototyping |
| Prefix tuning | <0.1% | Very low | Good for specific tasks | Constrained resources |
| Adapters | ~2-4% | Low | Good | Multi-task serving |

## 2. Full Fine-Tuning

Update all model parameters on the downstream dataset.

- Requires storing full model + optimiser states + gradients in memory
- For a 7B model in FP16: ~14 GB for weights + ~56 GB for Adam optimiser states = ~70 GB minimum
- Risk of **catastrophic forgetting** (model loses pre-trained capabilities)
- Mitigations: low learning rate, short training, mixing in pre-training data

**When to use**: large, high-quality datasets (>10K examples), when maximum performance matters, and you have sufficient compute.

## 3. LoRA (Low-Rank Adaptation)

The most widely used parameter-efficient fine-tuning (PEFT) method.

### Core Idea
Instead of updating a weight matrix $W \in \mathbb{R}^{d \times k}$ directly, learn a low-rank decomposition of the update:

$$
W' = W + \Delta W = W + BA
$$

Where $B \in \mathbb{R}^{d \times r}$ and $A \in \mathbb{R}^{r \times k}$, with rank $r \ll \min(d, k)$.

- $W$ is **frozen** (no gradients stored)
- Only $A$ and $B$ are trained
- Trainable parameters: $r \times (d + k)$ instead of $d \times k$
- For $d = k = 4096$ and $r = 16$: 131K params vs 16.8M (128× reduction)

### Key Hyperparameters

| Param | Typical values | Effect |
|:-----:|:--------------:|:------:|
| $r$ (rank) | 8, 16, 32, 64 | Higher = more capacity but more params. 16 is a common default |
| $\alpha$ (scaling) | 16, 32 | Scales the update: $\Delta W = \frac{\alpha}{r} BA$. Controls magnitude of adaptation |
| Target modules | q_proj, v_proj (minimal); all linear layers (full) | Which weight matrices to adapt |
| Dropout | 0.05-0.1 | Regularisation on the LoRA layers |

### Why It Works
- Pre-trained weight matrices are full-rank, but the **task-specific update** often has low intrinsic rank
- The adaptation for a downstream task doesn't need to modify the full weight space — a small subspace suffices
- At inference, $BA$ can be merged into $W$ with no additional latency

### Initialisation
- $A$ is initialised with random Gaussian
- $B$ is initialised to **zero** → $\Delta W = 0$ at the start, so training begins from the pre-trained model exactly

> **Source**: Hu et al., "LoRA: Low-Rank Adaptation of Large Language Models" (2021)

## 4. QLoRA (Quantised LoRA)

Combines LoRA with aggressive quantisation to enable fine-tuning on consumer GPUs.

### Key Innovations
1. **4-bit NormalFloat (NF4)**: a quantisation format optimised for normally distributed weights (which neural network weights approximately are)
2. **Double quantisation**: quantise the quantisation constants themselves, saving ~0.4 bits per parameter
3. **Paged optimisers**: use CPU memory as overflow for GPU memory spikes

### Memory Comparison (7B model)

| Method | GPU Memory |
|:------:|:----------:|
| Full fine-tuning (FP16) | ~70 GB |
| LoRA (FP16 base) | ~16 GB |
| QLoRA (NF4 base) | ~6 GB |

- QLoRA matches LoRA quality in practice (sometimes within 0.1% on benchmarks)
- Enables fine-tuning a 65B model on a single 48 GB GPU

> **Source**: Dettmers et al., "QLoRA: Efficient Finetuning of Quantized Language Models" (2023)

## 5. Adapters

Insert small trainable modules **between** existing layers, keeping original weights frozen.

### Architecture
```
Input → Frozen Layer → [Adapter: Down-project → ReLU → Up-project] → Residual Add → Output
```

- Down-project: $d_{model} \to d_{bottleneck}$ (e.g., 768 → 64)
- Up-project: $d_{bottleneck} \to d_{model}$
- Residual connection around the adapter (so it can learn the identity function)

### Advantages
- Multiple adapters for different tasks can share the same base model
- Swap adapters at inference time for multi-task serving
- ~2-4% of total parameters

> **Source**: Houlsby et al., "Parameter-Efficient Transfer Learning for NLP" (2019)

## 6. Prefix Tuning / Prompt Tuning

### Prefix Tuning
Prepend learnable "virtual tokens" to the Key and Value matrices at every attention layer:
- These virtual tokens are not real embeddings — they're learnable continuous vectors
- Only the prefix parameters are trained (~0.1% of total)
- The model's weights remain completely frozen

> **Source**: Li & Liang, "Prefix-Tuning: Optimizing Continuous Prompts for Generation" (2021)

### Prompt Tuning
A simpler variant: prepend learnable embeddings only at the input layer:
- Even fewer parameters than prefix tuning
- Performance approaches full fine-tuning as model size increases (Lester et al. showed convergence at ~10B parameters)

> **Source**: Lester et al., "The Power of Scale for Parameter-Efficient Prompt Tuning" (2021)

## 7. Practical Decision Guide

```
Do you have >100K high-quality examples?
├── Yes → Full fine-tuning (if you have compute) or LoRA
└── No
    ├── Do you have a large GPU (≥24 GB)?
    │   └── LoRA
    └── Consumer GPU (<16 GB)?
        └── QLoRA
```

### Tips
- **Start with LoRA** — it's the best default. Only move to full fine-tuning if LoRA is clearly insufficient
- **Data quality > quantity**: 1K high-quality examples often beats 100K noisy ones
- **Evaluation**: always hold out a test set. Monitor for catastrophic forgetting by evaluating on general benchmarks too
- **Learning rate**: use 1e-4 to 2e-4 for LoRA (10× higher than full fine-tuning)
- **Epochs**: 1-3 epochs is usually sufficient; more risks overfitting

## 8. Common Interview Questions

- **Q: How does LoRA reduce memory usage?** By freezing the original weights (no gradients/optimiser states needed for them) and only training two small matrices $A$ and $B$. The rank $r$ is much smaller than the weight dimensions, so the trainable parameter count drops by 100×+. At inference, $BA$ merges into $W$ with zero overhead.

- **Q: When would you choose full fine-tuning over LoRA?** When you have abundant high-quality data, sufficient compute, and need maximum performance. Also when the task requires significant distribution shift from the base model (e.g., adapting an English model to a very different language). In practice, LoRA is sufficient for most use cases.

- **Q: What is catastrophic forgetting and how do you prevent it?** The model loses previously learned capabilities when fine-tuned on a narrow dataset. Prevention: use a low learning rate, train for few epochs, mix in pre-training data, use LoRA (which preserves original weights), or apply elastic weight consolidation (EWC) to penalise large weight changes.

- **Q: Can you serve multiple LoRA adapters from the same base model?** Yes — this is a key advantage. The base model stays in GPU memory once, and you swap lightweight LoRA weights per request. Libraries like vLLM and LoRAX support this natively, enabling multi-tenant serving with minimal memory overhead.
