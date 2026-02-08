#transformer #spatialtransformer #computervision

# Spatial Transformer Networks (STN)

**References**:
- PyTorch tutorial: https://pytorch.org/tutorials/intermediate/spatial_transformer_tutorial.html
- Explainer: https://towardsdatascience.com/spatial-transformer-networks-b743c0d112be

## 1. Core Idea

Spatial Transformer modules learn to **spatially transform** their input (translation, rotation, scaling, cropping, non-rigid deformations) to a canonical, expected pose. This makes the network **spatially invariant** without needing data augmentation or hand-crafted preprocessing.

- Can be inserted into existing CNN architectures at any point
- Fully differentiable — trained end-to-end with backpropagation
- Each input sample gets its own transformation, **conditioned on the input itself** (adaptive)

**Analogy**: a pre-processing step that learns to "straighten out" tilted digits, crop to the relevant region, or undo perspective distortion — but learned jointly with the task.

> **Source**: Jaderberg et al., "Spatial Transformer Networks" (2015)

## 2. Architecture (Three Components)

```
Input Feature Map (U) → [Localisation Network] → θ (transformation params)
                                                        ↓
                                               [Grid Generator] → sampling grid (Tθ)
                                                        ↓
Input Feature Map (U) ────────────────────────→ [Sampler] → Output Feature Map (V)
```

### 2.1 Localisation Network
- Takes the input feature map $U$ and outputs the transformation parameters $\theta$
- Can be any architecture: a small CNN or fully-connected network
- For a 2D affine transformation, outputs 6 parameters (2×3 matrix):

$$
\theta = \begin{pmatrix} \theta_{11} & \theta_{12} & \theta_{13} \\ \theta_{21} & \theta_{22} & \theta_{23} \end{pmatrix}
$$

- Initialised close to identity so the network starts with no transformation and learns to add one

### 2.2 Grid Generator
- Creates a sampling grid: for each output pixel location, computes where to sample from in the input
- Applies the transformation $\theta$ to a regular grid of output coordinates:

$$
\begin{pmatrix} x_i^s \\ y_i^s \end{pmatrix} = \begin{pmatrix} \theta_{11} & \theta_{12} & \theta_{13} \\ \theta_{21} & \theta_{22} & \theta_{23} \end{pmatrix} \begin{pmatrix} x_i^t \\ y_i^t \\ 1 \end{pmatrix}
$$

Where $(x_i^t, y_i^t)$ are the target (output) coordinates and $(x_i^s, y_i^s)$ are the corresponding source (input) coordinates.

### 2.3 Sampler (Differentiable)
- Samples the input feature map at the computed source coordinates using **bilinear interpolation**
- This is the key to making the entire module differentiable:

$$
V_i = \sum_n \sum_m U_{nm} \max(0, 1 - |x_i^s - m|) \max(0, 1 - |y_i^s - n|)
$$

- Gradients flow back through the sampler to the grid generator and localisation network

> **Source**: Jaderberg et al. (2015), Sections 3.1–3.3

## 3. Types of Transformations

| Transformation | Parameters | Degrees of Freedom | What It Can Do |
|:--------------:|:----------:|:------------------:|:--------------:|
| Affine | 2×3 matrix | 6 | Translation, rotation, scaling, shearing |
| Projective | 3×3 matrix | 8 | Perspective distortion |
| Thin Plate Spline | Control points | Variable | Non-rigid, local deformations |
| Attention (cropping) | 4 params | 4 | Isotropic scaling + translation (zoom & crop) |

The attention variant (scale + translate only) is the simplest and most commonly used:

$$
\theta = \begin{pmatrix} s & 0 & t_x \\ 0 & s & t_y \end{pmatrix}
$$

## 4. Key Properties

- **Differentiable**: the entire module is differentiable, so it can be trained with standard backprop — no special loss required
- **Self-contained**: the STN decides what transformation to apply based on the input; no supervision of the transformation is needed
- **Stackable**: multiple STN modules can be placed in sequence or in parallel (e.g., one per object in a scene)
- **Channel-agnostic**: applies the same spatial transformation to all channels of the feature map

## 5. Use Cases

- **Digit recognition**: learning to undo rotation/translation of handwritten digits (MNIST)
- **Fine-grained recognition**: zooming into the discriminative part of an image (e.g., bird species → zoom into the head)
- **OCR / document processing**: correcting perspective distortion in scanned text
- **Pose normalisation**: aligning faces or body parts before classification

## 6. STN vs Data Augmentation

| Aspect | STN | Data Augmentation |
|:------:|:---:|:-----------------:|
| When applied | At inference time (adaptive per sample) | Only during training |
| Learned | Yes (end-to-end) | No (hand-designed) |
| Input-dependent | Yes (each sample gets its own transform) | No (random transforms) |
| Compute cost | Adds a small network + grid sampling | No inference cost |
| Flexibility | Can learn task-relevant transforms | Limited to pre-defined transforms |

## 7. Common Interview Questions

- **Q: How is the spatial transformer differentiable?** The key is the bilinear interpolation sampler. Since bilinear interpolation is a weighted sum of neighbouring pixels with weights that are differentiable functions of the sampling coordinates, gradients can flow from the output back through the sampler to the grid generator and localisation network.

- **Q: Does the STN need explicit supervision for the transformation?** No. The transformation parameters are learned implicitly through the task loss (e.g., classification cross-entropy). The network learns whatever spatial transformation helps the downstream task.

- **Q: Where should you place an STN in a network?** Typically right after the input (to normalise the raw image) or after early convolutional layers (to normalise learned features). Multiple STNs can be placed at different depths.

- **Q: What's the difference between spatial transformers and attention?** Spatial transformers apply a global geometric transformation (affine, projective) to the entire feature map. Self-attention computes content-based soft weights between all positions. STNs transform "where to look"; attention determines "what to focus on". They are complementary.
