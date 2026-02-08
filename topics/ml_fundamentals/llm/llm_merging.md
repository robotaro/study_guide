#llm #llmmerging #llmleaderboard

# LLM Merging

**Reference video**: https://www.youtube.com/watch?v=byf-y0P4hMg
**Leaderboard**: https://huggingface.co/spaces/HuggingFaceH4/open_llm_leaderboard

## 1. Overview

LLM merging combines weights from multiple pre-trained or fine-tuned models into a single model **without any additional training**. This is useful for combining capabilities from specialist models (e.g., one fine-tuned for code, another for chat) into a generalist.

### Requirements
- Models must have the **same architecture** (e.g., all LLaMA, all Mistral)
- Models must have the **same number of layers** and hidden dimensions
- Models typically share a common base model (e.g., both fine-tuned from LLaMA-2-7B)

### Common Architectures for Merging
- LLaMA / LLaMA 2 / LLaMA 3
- Mistral / Mixtral
- Phi

## 2. Merging Methods

### 2.1 Linear (Weight Averaging)
The simplest approach: weighted average of model parameters.

$$
\theta_{merged} = \alpha \cdot \theta_A + (1 - \alpha) \cdot \theta_B
$$

- $\alpha$ controls the blend (0.5 = equal contribution)
- Works surprisingly well when models are fine-tuned from the same base
- Limitation: doesn't account for parameter importance

### 2.2 Task Arithmetic
Manipulate models using arithmetic on **task vectors**. A task vector is the difference between a fine-tuned model and the base model:

$$
\tau_A = \theta_A^{ft} - \theta_{base}
$$

You can then compose capabilities:

$$
\theta_{merged} = \theta_{base} + \lambda_A \cdot \tau_A + \lambda_B \cdot \tau_B
$$

- **Addition**: combine skills from multiple fine-tuned models
- **Negation**: $\theta_{base} - \lambda \cdot \tau_{toxic}$ removes undesirable behaviours
- $\lambda$ controls the strength of each task

> **Source**: Ilharco et al., "Editing Models with Task Arithmetic" (2023)

### 2.3 SLERP (Spherical Linear Interpolation)
Interpolates between two models along the surface of a hypersphere rather than in a straight line:

$$
\theta_{merged} = \frac{\sin((1-t) \cdot \Omega)}{\sin(\Omega)} \cdot \theta_A + \frac{\sin(t \cdot \Omega)}{\sin(\Omega)} \cdot \theta_B
$$

Where $\Omega = \arccos(\frac{\theta_A \cdot \theta_B}{||\theta_A|| \cdot ||\theta_B||})$ and $t \in [0, 1]$.

- Preserves the magnitude of weight vectors (unlike linear interpolation which can shrink them)
- Only works with **two models** at a time (not multiple)
- Often produces higher quality merges than simple averaging

### 2.4 TIES (Trim, Elect Sign, Merge)
Addresses interference between task vectors when merging multiple models:

1. **Trim**: zero out small-magnitude changes (keep only the top-k% most significant parameter changes)
2. **Elect sign**: for each parameter, resolve sign conflicts by majority vote across task vectors
3. **Merge**: average only the task vectors that agree on the elected sign

- Reduces parameter interference that degrades performance in naive merging
- Supports merging **multiple models**

> **Source**: Yadav et al., "Resolving Interference When Merging Models" (TIES, 2023)

### 2.5 DARE (Drop And REscale)
Randomly drops a fraction of delta parameters (differences from base) and rescales the remaining ones:

1. Drop each delta parameter with probability $p$
2. Rescale remaining deltas by $\frac{1}{1-p}$ to preserve expected magnitude
3. Merge the sparsified task vectors

- Often combined with TIES (DARE-TIES)
- The sparsification reduces interference between task vectors
- Drop rates of 90-99% often work, suggesting most fine-tuning deltas are redundant

> **Source**: Yu et al., "Language Models are Super Mario: Absorbing Abilities from Homologous Models as a Free Lunch" (DARE, 2023)

### 2.6 Passthrough (Frankenmerging)
Stacks layers from different models sequentially rather than combining weights:
- Take layers 0-15 from Model A, layers 16-31 from Model B
- Creates a model with a **different number of layers** than the originals
- Can create larger models from smaller ones (e.g., two 7B models → one "14B" model)
- No mathematical guarantee of coherence; results are empirical

## 3. Summary Table

| Method | Multiple models | Requires base model | Key idea |
|:------:|:---------------:|:-------------------:|:--------:|
| Linear | Yes | No | Weighted average |
| Task Arithmetic | Yes | Yes | Arithmetic on fine-tuning deltas |
| SLERP | No (2 only) | No | Spherical interpolation preserves magnitude |
| TIES | Yes | Yes | Resolve sign conflicts, trim small deltas |
| DARE | Yes | Yes | Random sparsification of deltas |
| Passthrough | Yes | No | Stack layers from different models |

## 4. Practical Tools
- **mergekit** (arcee-ai): the standard open-source tool for model merging. Supports all methods above, runs on CPU
- **LazyMergekit**: Colab notebook wrapper for mergekit

## 5. Common Interview Questions

- **Q: Why does model merging work at all?** Models fine-tuned from the same base share a common loss landscape. Fine-tuning moves weights to nearby basins, so interpolating between them often stays in a good region. The "linear mode connectivity" hypothesis suggests that fine-tuned models connected by low-loss paths in weight space can be merged without catastrophic degradation.

- **Q: When would you merge vs fine-tune?** Merge when you want to combine capabilities cheaply (no GPU training needed) and models share a base. Fine-tune when you need precise control, have specific training data, or the capabilities you want can't be achieved by combining existing models.

- **Q: What's the difference between TIES and simple averaging?** Simple averaging treats all parameter changes equally and can suffer from sign conflicts (one model pushes a weight up, another pushes it down — averaging cancels both). TIES resolves this by trimming small changes, voting on the sign, and only merging agreeing parameters.
