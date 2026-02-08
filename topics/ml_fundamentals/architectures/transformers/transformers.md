#transformer #llm #architecture

# The Transformer Architecture

**Reference video**: https://www.youtube.com/watch?v=frosrL1CEhw
- Watch again: especially the part explaining how attention changes the weights of key embeddings for a query

## 1. Overview

The Transformer (Vaswani et al., 2017) replaced recurrence and convolutions entirely with attention. It is the backbone of virtually all modern NLP and increasingly vision models.

**Key advantages over RNNs/LSTMs:**
- **Parallelisable**: all positions processed simultaneously (no sequential dependency)
- **Long-range dependencies**: direct connections between any two positions (O(1) path length vs O(n) for RNNs)
- **Scalable**: scales efficiently to billions of parameters with modern hardware

> **Source**: Vaswani et al., "Attention Is All You Need" (2017)

## 2. Architecture (Encoder-Decoder)

The original Transformer has two main components:

```
Input → [Encoder × N] → Encoded Representation
                                  ↓ (cross-attention)
Output (shifted right) → [Decoder × N] → Linear → Softmax → Predictions
```

### Encoder (× N layers, original N=6)
Each layer has two sub-layers:
1. **Multi-Head Self-Attention** — each token attends to all tokens in the input
2. **Position-wise Feed-Forward Network (FFN)** — two linear layers with ReLU in between

Each sub-layer has a **residual connection** and **layer normalisation**:

$$
\text{output} = \text{LayerNorm}(x + \text{SubLayer}(x))
$$

### Decoder (× N layers, original N=6)
Each layer has three sub-layers:
1. **Masked Multi-Head Self-Attention** — causal mask prevents attending to future tokens
2. **Multi-Head Cross-Attention** — queries from decoder, keys/values from encoder output
3. **Position-wise FFN** — same structure as encoder

Same residual connections and layer norm around each sub-layer.

### Feed-Forward Network (FFN)
Applied independently to each position (same parameters across positions, different across layers):

$$
\text{FFN}(x) = \max(0, xW_1 + b_1)W_2 + b_2
$$

- Inner dimension is typically 4× the model dimension (e.g., $d_{model}=512$, $d_{ff}=2048$)
- This is where most of the model's parameters live

> **Source**: Vaswani et al. (2017), Section 3.3

## 3. Positional Encoding

Self-attention is **permutation equivariant** — it has no notion of token order. Positional encodings inject sequence position information.

### Sinusoidal (Original)
Fixed, non-learned encodings using sine and cosine functions at different frequencies:

$$
PE_{(pos, 2i)} = \sin\left(\frac{pos}{10000^{2i/d_{model}}}\right)
$$

$$
PE_{(pos, 2i+1)} = \cos\left(\frac{pos}{10000^{2i/d_{model}}}\right)
$$

- Each dimension corresponds to a different frequency
- The model can learn to attend to relative positions because $PE_{pos+k}$ can be expressed as a linear function of $PE_{pos}$
- Generalises to sequence lengths not seen during training

### Learned Positional Embeddings
- A learnable embedding matrix of shape (max_seq_len, $d_{model}$)
- Used in BERT, GPT-2
- Cannot generalise beyond the max training length without extrapolation tricks

### Rotary Position Embedding (RoPE)
- Encodes position by rotating the Q and K vectors in 2D subspaces
- Naturally encodes **relative** position: the dot product between rotated Q and K depends only on the relative distance
- Used in LLaMA, PaLM, and most modern LLMs
- Better length generalisation than learned embeddings

> **Sources**: Vaswani et al. (2017), Section 3.5; Su et al., "RoFormer: Enhanced Transformer with Rotary Position Embedding" (2021)

## 4. Training Details

### Label Smoothing
- Instead of hard 0/1 targets, distribute a small probability $\epsilon$ across all classes
- Prevents overconfident predictions, improves generalisation
- Original paper uses $\epsilon = 0.1$

### Learning Rate Schedule (Warmup + Decay)
The original Transformer uses a specific schedule:

$$
lr = d_{model}^{-0.5} \cdot \min(step^{-0.5}, step \cdot warmup\_steps^{-1.5})
$$

- Linear warmup for the first $warmup\_steps$ (original: 4000)
- Then decay proportional to the inverse square root of step number
- This stabilises early training when the model parameters are randomly initialised

### Dropout
Applied to:
- Attention weights (after softmax)
- Output of each sub-layer (before residual addition)
- Positional encoding sums
- Original paper uses $p_{drop} = 0.1$

> **Source**: Vaswani et al. (2017), Section 5

## 5. Architectural Variants

### Encoder-Only (BERT family)
- Bidirectional self-attention (no causal mask)
- Pre-trained with Masked Language Modelling (MLM): predict randomly masked tokens
- Best for: classification, NER, sentence embeddings, retrieval
- Examples: BERT, RoBERTa, DeBERTa

### Decoder-Only (GPT family)
- Causal (masked) self-attention only
- Pre-trained with next-token prediction (autoregressive language modelling)
- Best for: text generation, in-context learning, general-purpose LLMs
- Examples: GPT-2/3/4, LLaMA, PaLM, Mistral

### Encoder-Decoder (Original / T5 family)
- Full architecture as described above
- Best for: seq2seq tasks (translation, summarisation)
- Examples: original Transformer, T5, BART, mBART

| Variant | Attention Type | Pre-training Objective | Strengths |
|:-------:|:--------------:|:---------------------:|:---------:|
| Encoder-only | Bidirectional | MLM | Understanding, classification |
| Decoder-only | Causal | Next-token prediction | Generation, few-shot learning |
| Encoder-decoder | Both | Span corruption / denoising | Seq2seq, translation |

> **Sources**: Devlin et al., "BERT: Pre-training of Deep Bidirectional Transformers" (2018); Radford et al., "Language Models are Unsupervised Multitask Learners" (GPT-2, 2019); Raffel et al., "Exploring the Limits of Transfer Learning with a Unified Text-to-Text Transformer" (T5, 2019)

## 6. Key Design Choices and Their Impact

| Design Choice | Purpose |
|:-------------:|:-------:|
| Residual connections | Enable gradient flow through deep networks; allow layers to learn incremental refinements |
| Layer normalisation | Stabilise activations; reduce internal covariate shift |
| Multi-head attention | Learn diverse attention patterns in parallel |
| FFN inner expansion (4×) | Provide capacity for storing factual knowledge and performing non-linear transformations |
| Dropout on attention weights | Regularisation; prevents over-reliance on specific token relationships |

## 7. Pre-Norm vs Post-Norm

The original Transformer uses **Post-Norm** (norm after residual add):

$$
\text{Post-Norm}: \quad \text{LayerNorm}(x + \text{SubLayer}(x))
$$

Most modern implementations use **Pre-Norm** (norm before the sub-layer):

$$
\text{Pre-Norm}: \quad x + \text{SubLayer}(\text{LayerNorm}(x))
$$

- Pre-Norm is more stable during training (gradients flow more easily through the residual path)
- Post-Norm can achieve slightly better final performance but requires careful warmup
- GPT-2, LLaMA, and most recent LLMs use Pre-Norm

> **Source**: Xiong et al., "On Layer Normalization in the Transformer Architecture" (2020)

## 8. Common Interview Questions

- **Q: Walk through the full forward pass of a Transformer encoder layer.** Input → layer norm → multi-head self-attention → residual add → layer norm → FFN → residual add → output. (Pre-Norm variant. Post-Norm swaps the order of norm and sub-layer.)

- **Q: Why residual connections?** They allow gradients to flow directly through the network without vanishing. Each layer learns a residual/delta rather than a complete transformation, making optimisation much easier for deep networks.

- **Q: Why does the FFN use a 4× expansion?** The expanded inner dimension provides more capacity for the model to learn complex non-linear mappings. Recent work (e.g., Mixture of Experts) suggests the FFN acts as a key-value memory storing factual knowledge.

- **Q: Encoder-only vs decoder-only — when to use which?** Encoder-only (BERT) when you need bidirectional context for understanding tasks (classification, retrieval). Decoder-only (GPT) when you need generation or want a single model for many tasks via prompting. Encoder-decoder (T5) for explicit input→output mappings like translation.

- **Q: What is the parameter count dominated by?** The FFN layers and the embedding/unembedding matrices. Attention projection matrices ($W_Q, W_K, W_V, W_O$) are smaller. For a model with $d_{model}=512$ and $d_{ff}=2048$, each FFN has $2 \times 512 \times 2048 = 2M$ params vs $4 \times 512^2 = 1M$ for all four attention projections.
