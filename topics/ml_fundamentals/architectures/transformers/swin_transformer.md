#transformer #imagesegmentation #computervision

# Swin Transformer

**Reference**: https://huggingface.co/docs/transformers/en/model_doc/swin

## 1. Core Idea

The main limitation of ViT for dense prediction tasks (detection, segmentation) is that it produces single-scale features and has quadratic complexity with image size. Swin Transformer solves both problems with **hierarchical feature maps** and **shifted window attention**.

- Computes attention **within local windows** instead of globally → linear complexity with image size
- **Shifts the window partition** between layers to allow cross-window connections
- Builds a **multi-scale feature pyramid** (like CNNs) by merging patches at each stage

> **Source**: Liu et al., "Swin Transformer: Hierarchical Vision Transformer using Shifted Windows" (2021)

## 2. Architecture

```
Image → Patch Partition (4×4) → Stage 1 → Patch Merging → Stage 2 → Patch Merging → Stage 3 → Patch Merging → Stage 4
              ↓                    ↓                         ↓                         ↓                         ↓
         H/4 × W/4 × C       H/4 × W/4 × C            H/8 × W/8 × 2C          H/16 × W/16 × 4C         H/32 × W/32 × 8C
```

### Stages
1. **Patch partition**: split image into non-overlapping 4×4 patches (smaller than ViT's 16×16)
2. **Each stage**: stack of Swin Transformer blocks (alternating W-MSA and SW-MSA)
3. **Patch merging** (between stages): concatenate 2×2 neighbouring patches and project down, reducing spatial resolution by 2× and doubling channels — analogous to strided convolution / pooling in CNNs

This produces a feature pyramid at 4 resolutions, directly compatible with FPN, UNet, and other multi-scale architectures.

## 3. Window Attention (W-MSA)

Instead of global self-attention over all $HW/P^2$ tokens:
- Partition the feature map into non-overlapping **windows** of $M \times M$ patches (default $M=7$)
- Compute self-attention independently within each window

**Complexity comparison** (for an image with $h \times w$ patches):

$$
\text{Global MSA}: \quad O((hw)^2 \cdot d)
$$

$$
\text{Window MSA}: \quad O(hw \cdot M^2 \cdot d)
$$

Window attention is **linear** in image size ($hw$) since $M$ is fixed.

**Problem**: windows don't communicate with each other → no cross-window information flow.

## 4. Shifted Window Attention (SW-MSA)

The key innovation. In alternating layers:
- **Layer $l$**: regular window partition
- **Layer $l+1$**: shift the window partition by $(\lfloor M/2 \rfloor, \lfloor M/2 \rfloor)$ pixels

This means patches that were at the boundary of different windows in layer $l$ are now in the **same** window in layer $l+1$, enabling cross-window connections.

### Efficient Implementation
- Shifting creates uneven windows at borders
- Solved with **cyclic shifting** + **attention masking**: cyclically shift the feature map, apply regular windowed attention with masks to prevent attention between non-adjacent regions, then shift back
- This avoids padding and keeps the computation uniform

> **Source**: Liu et al. (2021), Section 3.2

## 5. Relative Position Bias

Instead of absolute positional embeddings (like ViT), Swin uses a **learnable relative position bias** $B$ added to the attention scores:

$$
\text{Attention}(Q, K, V) = \text{softmax}\left(\frac{QK^T}{\sqrt{d_k}} + B\right) V
$$

- $B$ is indexed by the relative position between each pair of tokens within a window
- For a window of $M \times M$, relative positions range from $[-(M-1), M-1]$ in each axis
- The bias matrix has $(2M-1) \times (2M-1)$ learnable entries
- This significantly improves performance over absolute or no positional encoding

## 6. Model Configurations

| Model | Layers per stage | Channels ($C$) | Params |
|:-----:|:----------------:|:--------:|:------:|
| Swin-T (Tiny) | [2, 2, 6, 2] | 96 | 28M |
| Swin-S (Small) | [2, 2, 18, 2] | 96 | 50M |
| Swin-B (Base) | [2, 2, 18, 2] | 128 | 88M |
| Swin-L (Large) | [2, 2, 18, 2] | 192 | 197M |

> **Source**: Liu et al. (2021), Table 1

## 7. Swin vs ViT

| Aspect | ViT | Swin |
|:------:|:---:|:----:|
| Attention scope | Global (all patches) | Local (within windows) |
| Complexity | $O(n^2)$ in sequence length | $O(n)$ in image size (linear) |
| Feature maps | Single scale | Multi-scale pyramid |
| Position encoding | Absolute (learned or sinusoidal) | Relative position bias |
| Best for | Classification (with large pre-training) | Dense tasks: detection, segmentation |
| Resolution flexibility | Fixed patch count at training resolution | Handles varying resolutions naturally |

## 8. Downstream Applications

Swin Transformer is used as a **general-purpose vision backbone** for:
- **Image classification**: competitive with ViT on ImageNet
- **Object detection**: backbone for Mask R-CNN, Cascade R-CNN (via FPN on multi-scale features)
- **Semantic segmentation**: backbone for UPerNet, achieves strong results on ADE20K
- **Instance segmentation**: backbone for Mask2Former
- **Video**: Swin-V2 (Liu et al., 2022) extended with 3D shifted windows for video understanding

> **Source**: Liu et al., "Swin Transformer V2: Scaling Up Capacity and Resolution" (2022)

## 9. Common Interview Questions

- **Q: Why not just use global attention like ViT?** Global attention is $O(n^2)$ with the number of patches. For high-resolution images or dense prediction, the sequence length is very large (e.g., 3136 patches for 896×896 with 16×16 patches). Window attention keeps it manageable and linear in image size.

- **Q: How does information flow between windows?** Through shifted window attention. By alternating between regular and shifted window partitions, patches at window boundaries interact in alternating layers, effectively creating cross-window connections across the network depth.

- **Q: Why is Swin better than ViT for detection/segmentation?** Two reasons: (1) multi-scale feature maps from patch merging, which dense prediction heads (FPN, UNet) expect, and (2) linear complexity, allowing higher-resolution inputs that detection/segmentation require.
