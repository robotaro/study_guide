# Prioritized Study Plan

Master study plan organized by ROI — topics that overlap across all 3 target roles (Meta, Bumble, Humanoid) come first.

---

## Tier 1 — Universal (all 3 roles)

### Coding & Data Structures / Algorithms

Core patterns and problem-solving fluency. Required by all three roles.

- [ ] Review array/string patterns — two pointers, sliding window, prefix sums ([notes](../topics/coding/concepts/arrays.md))
- [ ] Review binary trees — traversals, BFS/DFS, recursive patterns ([notes](../topics/coding/concepts/binary_trees.md))
- [ ] Review heaps / priority queues ([notes](../topics/coding/concepts/heaps.md), [min heap](../topics/coding/concepts/min_heap.md))
- [ ] Review dynamic programming — top-down vs bottom-up, common patterns ([notes](../topics/coding/concepts/dynamic_programming.md))
- [ ] Review divide and conquer ([notes](../topics/coding/concepts/divide_and_conquer.md))
- [ ] Study graph algorithms — BFS, DFS, topological sort, shortest path (no notes yet)
- [ ] Study binary search patterns — on arrays and on answer space (no notes yet)
- [ ] Study backtracking patterns (no notes yet)
- [ ] Study greedy algorithms (no notes yet)
- [ ] Review amortized time complexity ([notes](../topics/coding/concepts/amortized_time.md))
- [ ] Review Python-specific tips and idioms ([notes](../topics/coding/python_tips.md))
- [ ] Solve 5 medium array/string problems ([leetcode tracker](../topics/coding/leetcode/README.md))
- [ ] Solve 5 medium tree/graph problems ([leetcode tracker](../topics/coding/leetcode/README.md))
- [ ] Solve 5 medium DP problems ([leetcode tracker](../topics/coding/leetcode/README.md))
- [ ] Solve 3 medium sliding window problems ([leetcode tracker](../topics/coding/leetcode/README.md))
- [ ] Solve 3 medium heap problems ([leetcode tracker](../topics/coding/leetcode/README.md))
- [ ] Review complexity analysis — Big-O for time and space ([notes](../topics/coding/computer_science.md))

### ML Fundamentals

Core ML theory tested across all roles.

- [ ] Review training pipeline: loss functions, optimizers (SGD, Adam), learning rate schedules ([notes](../topics/ml_fundamentals/deep_learning.md))
- [ ] Review regularization: dropout, weight decay, batch norm, data augmentation ([notes](../topics/ml_fundamentals/neural_networks.md))
- [ ] Review evaluation metrics: precision, recall, F1, AUC-ROC, mAP ([notes](../topics/ml_fundamentals/deep_learning.md))
- [ ] Review deep learning basics: CNNs, pooling, activation functions ([notes](../topics/ml_fundamentals/neural_networks.md))
- [ ] Review RNNs, LSTMs, GRUs and when to use them ([notes](../topics/ml_fundamentals/deep_learning.md))
- [ ] Review attention mechanism and self-attention ([notes](../topics/ml_fundamentals/attention_mechanism.md))
- [ ] Review transformer architecture end-to-end ([notes](../topics/ml_fundamentals/transformers.md))
- [ ] Review classical ML: SVM, random forest, boosting, clustering ([notes](../topics/ml_fundamentals/classical_ml.md))
- [ ] Review overfitting vs underfitting — diagnosis and remedies ([notes](../topics/ml_fundamentals/deep_learning.md))
- [ ] Review bias-variance tradeoff (no notes yet)
- [ ] Review autoencoders and VAEs ([notes](../topics/ml_fundamentals/autoencoders_vaes.md))
- [ ] Review contrastive learning ([notes](../topics/ml_fundamentals/contrastive_learning.md))

### ML System Design

End-to-end pipeline design — tested at Meta and relevant for Bumble and Humanoid.

- [ ] Practice designing an end-to-end ML pipeline: problem framing → data → model → serving → monitoring (no notes yet)
- [ ] Study data collection and labeling strategies at scale (no notes yet)
- [ ] Study training at scale: distributed training, mixed precision, gradient accumulation (no notes yet)
- [ ] Study model serving and inference: batching, latency vs throughput, caching (no notes yet)
- [ ] Study monitoring and retraining: data drift, model drift, A/B testing (no notes yet)
- [ ] Practice 3 full system design mock problems (e.g., recommendation system, content moderation, search ranking) (no notes yet)

---

## Tier 2 — Role-Specific High Value

### LLM Serving & MLOps (Bumble)

- [ ] Study LLM serving frameworks: vLLM, TGI, TensorRT-LLM (no notes yet)
- [ ] Study Docker and Kubernetes basics for ML deployment (no notes yet)
- [ ] Study CI/CD pipelines for ML models (no notes yet)
- [ ] Study GPU serving: memory management, batching strategies, quantization (no notes yet)
- [ ] Study monitoring for LLM systems: latency, token throughput, cost tracking (no notes yet)
- [ ] Review LLM concepts and internals ([notes](../topics/ml_fundamentals/llm_concepts.md))

### Computer Vision & Perception (Humanoid)

- [ ] Review object detection architectures: YOLO, Faster R-CNN, SSD ([notes](../topics/ml_system_design/computer_vision.md))
- [ ] Review semantic segmentation: U-Net, DeepLab, FCN ([notes](../topics/ml_fundamentals/semantic_segmentation.md), [lecture](../topics/ml_system_design/semantic_segmentation_lecture.md))
- [ ] Review object tracking: SORT, DeepSORT, ByteTrack ([notes](../topics/ml_system_design/object_tracking_lecture_1.md), [lecture 2](../topics/ml_system_design/object_tracking_lecture_2.md))
- [ ] Study pose estimation: OpenPose, HRNet, MediaPipe (no notes yet)
- [ ] Study sensor fusion: camera + LiDAR + IMU (no notes yet)
- [ ] Study model optimization for embedded: pruning, quantization, knowledge distillation, ONNX, TensorRT (no notes yet)
- [ ] Review MOT and density filtering ([notes](../topics/ml_system_design/mot_density_filtering.md))
- [ ] Review Humanoid perception study guide ([notes](../topics/ml_system_design/humanoid_perception_study_guide.md))

### Behavioral (Meta)

- [ ] Prepare 5 STAR stories covering: leadership, conflict, failure, impact, collaboration ([notes](../topics/behavioral/meta_behavioral.md))
- [ ] Watch Dan Croitor Meta behavioral videos ([notes](../topics/behavioral/meta_behavioral.md))
- [ ] Practice telling each story in under 2 minutes
- [ ] Prepare "Why Meta?" and "Tell me about yourself" answers

---

## Tier 3 — Differentiators

### Diffusion Models & Imitation Learning (Humanoid)

- [ ] Review diffusion models vs autoregressive models ([notes](../topics/ml_fundamentals/diffusion_vs_autoregression.md))
- [ ] Study image generation with diffusion ([notes](../topics/ml_fundamentals/image_generation.md))
- [ ] Review diffusion course notes ([notes](../resources/lectures/diffusion_course/01_introduction.md))
- [ ] Study imitation learning: behavioral cloning, DAgger (no notes yet)

### Transformers in Vision & Robotics (Humanoid)

- [ ] Review Vision Transformers (ViT) ([notes](../topics/ml_fundamentals/visual_transformers.md))
- [ ] Review Swin Transformer ([notes](../topics/ml_fundamentals/swin_transformer.md))
- [ ] Review spatial transformers ([notes](../topics/ml_fundamentals/spatial_transformers.md))
- [ ] Study transformers applied to robotics / perception (no notes yet)

### Statistics & Probability (all roles, lower priority)

- [ ] Review probability basics: Bayes' theorem, conditional probability, distributions ([notes](../topics/statistics/probabilities.md))
- [ ] Study hypothesis testing: p-values, confidence intervals, A/B tests ([notes](../topics/statistics/probabilities.md))
- [ ] Review KL divergence ([notes](../topics/ml_fundamentals/kl_divergence.md))
- [ ] Review information theory basics: entropy, cross-entropy ([notes](../topics/ml_fundamentals/differential_entropy.md))

### LLM Internals (Bumble)

- [ ] Study tokenization, positional encoding, KV cache (no notes yet)
- [ ] Study RLHF, DPO, and alignment techniques (no notes yet)
- [ ] Review LLM merging techniques ([notes](../topics/ml_fundamentals/llm_merging.md))
- [ ] Review prompt engineering strategies ([notes](../topics/ml_fundamentals/prompt_engineering.md))
