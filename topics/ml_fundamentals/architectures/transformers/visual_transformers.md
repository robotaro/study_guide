#vit #visualtransformer #computervision

# Vision Transformer (ViT)

**Reference**: CogVLM2 (visual-language model) — https://github.com/THUDM/CogVLM2

## 1. Core Idea

Apply the Transformer architecture — originally designed for NLP — directly to images with minimal modifications. Instead of processing words/tokens, split the image into fixed-size patches and treat each patch as a "token".

**Key insight**: with enough data and compute, a pure Transformer (no convolutions at all) can match or beat CNNs on image classification.

> **Source**: Dosovitskiy et al., "An Image is Worth 16x16 Words: Transformers for End-to-End Object Recognition" (2020)

## 2. Architecture

```
Image → Split into patches → Linear projection → + Position embeddings → Transformer Encoder → Classification head
                                                   ↑
                                            [CLS] token prepended
```

### Step by Step

1. **Patch embedding**: split image of size $H \times W \times C$ into $N$ non-overlapping patches of size $P \times P$, giving $N = HW / P^2$ patches
2. **Flatten & project**: each patch is flattened to a vector of size $P^2 \cdot C$ and linearly projected to dimension $d_{model}$
3. **Prepend [CLS] token**: a learnable embedding prepended to the sequence; its output representation is used for classification
4. **Add positional embeddings**: learnable 1D position embeddings (one per patch + one for [CLS])
5. **Transformer encoder**: standard encoder with multi-head self-attention and FFN (typically 12 or 24 layers)
6. **Classification head**: MLP on the [CLS] token output (or global average pooling over patch tokens)

### Typical Configurations

| Model | Layers | Hidden size | Heads | Params | Patch size |
|:-----:|:------:|:-----------:|:-----:|:------:|:----------:|
| ViT-Base | 12 | 768 | 12 | 86M | 16×16 |
| ViT-Large | 24 | 1024 | 16 | 307M | 16×16 |
| ViT-Huge | 32 | 1280 | 16 | 632M | 14×14 |

> **Source**: Dosovitskiy et al. (2020), Table 1

## 3. Key Properties

### Data Hunger
- ViT **underperforms** CNNs when trained on mid-size datasets (e.g., ImageNet-1K alone)
- CNNs have built-in inductive biases (locality, translation equivariance) that ViT lacks
- ViT **excels** when pre-trained on very large datasets (ImageNet-21K, JFT-300M) and then fine-tuned
- With enough data, ViT learns the inductive biases that CNNs have hard-coded

### Computational Complexity
- Self-attention over $N$ patches is $O(N^2)$, but $N$ is typically small (e.g., 196 patches for 224×224 with 16×16 patches)
- This is far cheaper than pixel-level attention, which would be $O((HW)^2)$

### What ViT Learns
- Lower layers: local texture and edge patterns (similar to early CNN layers)
- Higher layers: global, semantic patterns across the entire image
- Attention heads specialise: some focus locally, others attend globally even in early layers

## 4. Important Variants and Successors

| Model | Key Innovation | Source |
|:-----:|:--------------:|:------:|
| DeiT | Data-efficient training with distillation token; trains ViT on ImageNet-1K alone | Touvron et al. (2021) |
| BEiT | BERT-style pre-training for vision: masked image modelling (predict masked patches) | Bao et al. (2021) |
| MAE | Masked Autoencoder: mask 75% of patches, reconstruct pixels. Very efficient pre-training | He et al. (2022) |
| DiNOv2 | Self-supervised ViT with self-distillation; strong general-purpose visual features | Oquab et al. (2023) |

### Vision-Language Models (VLMs)
ViT is now the standard vision encoder in multimodal models:
- **CLIP** (Radford et al., 2021): contrastive learning between image (ViT) and text (Transformer) encoders
- **LLaVA**: ViT encoder + LLM decoder for visual question answering
- **CogVLM2**: visual-language model with deep integration between vision and language

> **Sources**: Touvron et al., "Training data-efficient image transformers & distillation through attention" (DeiT, 2021); He et al., "Masked Autoencoders Are Scalable Vision Learners" (MAE, 2022); Radford et al., "Learning Transferable Visual Models From Natural Language Supervision" (CLIP, 2021)

## 5. ViT vs CNNs

| Aspect | ViT | CNN |
|:------:|:---:|:---:|
| Inductive bias | Minimal (only patch structure) | Strong (locality, translation equivariance, weight sharing) |
| Data efficiency | Needs large-scale pre-training | Works well on smaller datasets |
| Global context | From layer 1 (self-attention is global) | Only in deep layers (receptive field grows gradually) |
| Scalability | Scales well with data + compute | Diminishing returns at very large scale |
| Interpretability | Attention maps show what the model focuses on | Grad-CAM, feature maps |

## 6. Common Interview Questions

- **Q: Why split into patches instead of using pixels as tokens?** Pixel-level self-attention would be $O((HW)^2)$ — completely infeasible for typical image sizes. Patches reduce the sequence length to a manageable $N = HW/P^2$ (e.g., 196 for a 224×224 image with 16×16 patches).

- **Q: Why does ViT need more data than CNNs?** CNNs have hard-coded inductive biases (local connectivity, translation equivariance via weight sharing). ViT has to learn these patterns from data. With small datasets, the lack of these biases leads to overfitting.

- **Q: How is ViT adapted for dense prediction tasks (detection, segmentation)?** The [CLS]-based classification head is replaced. For detection: use patch tokens as a feature map and add a detection head (e.g., ViTDet). For segmentation: upsample patch token features or use a decoder (e.g., SegFormer, Mask2Former). Hierarchical variants like Swin Transformer are also popular for this.
