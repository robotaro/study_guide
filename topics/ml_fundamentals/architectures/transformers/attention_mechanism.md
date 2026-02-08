#transformer #llm #attention

# Attention Mechanism

## 1. Intuition

The core idea: not all inputs are equally relevant when producing an output. Attention lets the model dynamically focus on the most relevant parts of the input for each step of the output.

- **Without attention**: an encoder compresses the entire input into a single fixed-size vector. This bottleneck loses information, especially for long sequences.
- **With attention**: the model can look back at all encoder hidden states and weight them by relevance to the current decoding step.

**Analogy**: reading a question and scanning back through a paragraph to find the relevant sentence, rather than trying to memorise the whole paragraph first.

## 2. Original Attention (Seq2Seq Context)

Introduced by Bahdanau et al. (2014) for machine translation.

- The encoder produces a hidden state $h_i$ for each input token
- At each decoder step $t$, compute an **alignment score** between the decoder state $s_t$ and each encoder state $h_i$
- Normalise scores with softmax to get **attention weights** $\alpha_{t,i}$
- Compute a **context vector** $c_t$ as the weighted sum of encoder states

$$
e_{t,i} = \text{score}(s_t, h_i)
$$

$$
\alpha_{t,i} = \frac{\exp(e_{t,i})}{\sum_{j} \exp(e_{t,j})}
$$

$$
c_t = \sum_{i} \alpha_{t,i} \cdot h_i
$$

### Scoring Functions

| Name | Formula | Notes |
|:----:|:-------:|:-----:|
| Additive (Bahdanau) | $v^T \tanh(W_1 s_t + W_2 h_i)$ | Learnable params $W_1$, $W_2$, $v$. More expressive. |
| Dot-product (Luong) | $s_t^T h_i$ | No extra params. Faster. |
| Scaled dot-product | $\frac{s_t^T h_i}{\sqrt{d_k}}$ | Used in Transformers. Prevents softmax saturation for large $d_k$. |

> **Source**: Bahdanau et al., "Neural Machine Translation by Jointly Learning to Align and Translate" (2014); Luong et al., "Effective Approaches to Attention-based Neural Machine Translation" (2015)

## 3. Scaled Dot-Product Attention (Transformer Core)

From "Attention Is All You Need" (Vaswani et al., 2017). This is the attention variant used in all modern transformers.

### Query, Key, Value Framework

Every input token is projected into three vectors:
- **Query (Q)**: "what am I looking for?"
- **Key (K)**: "what do I contain?"
- **Value (V)**: "what information do I carry?"

Attention computes a weighted sum of Values, where each weight is determined by the compatibility between a Query and the corresponding Key.

$$
\text{Attention}(Q, K, V) = \text{softmax}\left(\frac{QK^T}{\sqrt{d_k}}\right) V
$$

Where $d_k$ is the dimensionality of the key vectors.

### Why scale by $\sqrt{d_k}$?

- The dot product $QK^T$ grows in magnitude with $d_k$ (the variance of each element sums up)
- Large dot products push softmax into regions with extremely small gradients
- Dividing by $\sqrt{d_k}$ keeps the variance of the dot products at ~1, keeping gradients healthy

### Step by Step

1. **Linear projections**: $Q = XW_Q$, $K = XW_K$, $V = XW_V$
2. **Compute scores**: $QK^T$ gives an $n \times n$ matrix of pairwise similarities
3. **Scale**: divide by $\sqrt{d_k}$
4. **Mask** (optional): set illegal positions to $-\infty$ before softmax
5. **Softmax**: row-wise normalisation into attention weights
6. **Weighted sum**: multiply weights by $V$ to get the output

> **Source**: Vaswani et al., "Attention Is All You Need" (2017)

## 4. Multi-Head Attention (MHA)

Instead of computing a single attention function, run $h$ attention heads in parallel, each with different learned projections. This allows the model to attend to information from different representation subspaces at different positions.

$$
\text{MultiHead}(Q, K, V) = \text{Concat}(\text{head}_1, \dots, \text{head}_h) W^O
$$

$$
\text{head}_i = \text{Attention}(QW_i^Q, KW_i^K, VW_i^V)
$$

- Typically $d_k = d_v = d_{model} / h$ so the total compute cost is similar to single-head attention at full dimensionality
- Common configurations: $d_{model}=512, h=8$ (original), $d_{model}=768, h=12$ (BERT-base)

**Why multiple heads?**
- One head might learn syntactic relationships, another semantic, another positional
- Provides redundancy and richer representations
- Single-head attention averages over all these signals into one set of weights

> **Source**: Vaswani et al. (2017), Section 3.2.2

## 5. Self-Attention vs Cross-Attention

### Self-Attention
- Q, K, V all come from the **same sequence**
- Each token attends to every other token (including itself) in the same sequence
- Used in: encoder layers, decoder masked self-attention
- Captures intra-sequence dependencies

### Cross-Attention
- Q comes from **one sequence** (decoder), K and V come from **another sequence** (encoder output)
- The decoder queries the encoder to find relevant input information
- Used in: encoder-decoder models (translation, summarisation, image captioning)

### Masked (Causal) Self-Attention
- Used in decoder / autoregressive models (GPT family)
- Masks future positions (sets them to $-\infty$ before softmax) so token $t$ can only attend to tokens $\leq t$
- Preserves the autoregressive property during training

## 6. Efficient Attention Variants

Standard self-attention is $O(n^2)$ in both time and memory (where $n$ is sequence length). This becomes a bottleneck for long sequences. Several variants address this:

| Variant | Complexity | Core Idea |
|:-------:|:----------:|:---------:|
| Sparse Attention | $O(n\sqrt{n})$ | Only attend to a fixed sparse pattern (local + strided). Used in GPT-3. |
| Linear Attention | $O(n)$ | Replace softmax with a kernel function; rearrange computation order so K,V are combined first. |
| Flash Attention | $O(n^2)$ time, reduced memory | Not an approximation; exact attention with tiled computation to minimise GPU memory reads/writes (IO-aware). |
| Multi-Query Attention (MQA) | Same complexity, faster inference | All heads share the same K and V projections. Reduces KV-cache size. |
| Grouped-Query Attention (GQA) | Between MHA and MQA | Groups of heads share K,V. Compromise between quality and speed. Used in Llama 2. |

### Flash Attention (Dao et al., 2022)
- The key insight is that attention is **memory-bound**, not compute-bound on modern GPUs
- Restructures the computation to avoid materialising the full $n \times n$ attention matrix in HBM (high-bandwidth memory)
- Uses **tiling** and **recomputation** to keep intermediate values in fast SRAM
- Gives exact results (not an approximation) with 2-4x speedup and significant memory savings

### Multi-Query / Grouped-Query Attention
- **MQA** (Shazeer, 2019): all attention heads share a single set of K and V projections, each head still has its own Q. Dramatically reduces KV-cache memory at inference
- **GQA** (Ainslie et al., 2023): a middle ground — heads are split into groups, each group shares K,V. Used in Llama 2 70B

> **Sources**: Child et al., "Generating Long Sequences with Sparse Transformers" (2019); Dao et al., "FlashAttention: Fast and Memory-Efficient Exact Attention with IO-Awareness" (2022); Shazeer, "Fast Transformer Decoding: One Write-Head is All You Need" (2019); Ainslie et al., "GQA: Training Generalized Multi-Query Transformer Models from Multi-Head Checkpoints" (2023)

## 7. Common Interview Questions

- **Q: Why use attention over RNNs?** Attention allows parallel processing of all positions (no sequential bottleneck), captures long-range dependencies directly (not through a chain of hidden states), and enables the model to focus on relevant parts regardless of distance.

- **Q: What is the complexity of self-attention?** $O(n^2 \cdot d)$ for time and $O(n^2)$ for memory, where $n$ is sequence length and $d$ is the model dimension. This quadratic scaling in $n$ is why long-context models need efficient attention variants.

- **Q: What happens if you remove the scaling factor?** For large $d_k$, dot products become large, pushing softmax into saturated regions where gradients are near zero. Training becomes unstable.

- **Q: Difference between additive and dot-product attention?** Additive (Bahdanau) uses a learned feed-forward network to compute scores — more expressive but slower. Dot-product (Luong/Vaswani) is simpler and benefits from optimised matrix multiplication. In practice, scaled dot-product dominates.
