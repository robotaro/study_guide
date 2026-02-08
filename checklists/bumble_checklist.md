# Bumble ML Engineer — Interview Prep Checklist

Organized by likely interview areas. See also: [Master Study Plan](study_plan.md)

---

## Coding (Python-focused)

- [ ] Review Python idioms: generators, decorators, context managers, comprehensions ([notes](../topics/coding/python_tips.md))
- [ ] Review data structures: dicts, sets, heaps, deques, defaultdict ([notes](../topics/coding/concepts/arrays.md))
- [ ] Solve 10 medium LeetCode problems with clean Pythonic solutions ([leetcode tracker](../topics/coding/leetcode/README.md))
- [ ] Solve 3 string/array problems emphasizing Python stdlib usage
- [ ] Practice writing clean, production-quality Python (type hints, error handling)
- [ ] Review complexity analysis ([notes](../topics/coding/computer_science.md))

---

## ML System Design / LLM Serving

### LLM serving fundamentals
- [ ] Study vLLM: PagedAttention, continuous batching, throughput optimization (no notes yet)
- [ ] Study TGI (Text Generation Inference) by HuggingFace (no notes yet)
- [ ] Study TensorRT-LLM: optimization for NVIDIA GPUs (no notes yet)
- [ ] Understand KV cache management and memory optimization (no notes yet)
- [ ] Study quantization techniques: INT8, INT4, GPTQ, AWQ (no notes yet)

### System design practice
- [ ] Design an LLM-powered chatbot serving system (no notes yet)
- [ ] Design a content moderation system using LLMs (no notes yet)
- [ ] Design a recommendation system with LLM-based embeddings (no notes yet)

### Key concepts
- [ ] Review LLM concepts: tokenization, attention, decoding strategies ([notes](../topics/ml_fundamentals/llm_concepts.md))
- [ ] Review transformer architecture in depth ([notes](../topics/ml_fundamentals/transformers.md))
- [ ] Review attention mechanism ([notes](../topics/ml_fundamentals/attention_mechanism.md))

---

## MLOps & Infrastructure

- [ ] Study Docker basics: Dockerfile, multi-stage builds, GPU containers (no notes yet)
- [ ] Study Kubernetes for ML: pods, deployments, GPU scheduling, HPA (no notes yet)
- [ ] Study CI/CD for ML: model versioning, automated testing, rollback strategies (no notes yet)
- [ ] Study model monitoring: latency tracking, output quality, drift detection (no notes yet)
- [ ] Study experiment tracking: MLflow, Weights & Biases, or similar (no notes yet)
- [ ] Study GPU serving economics: cost optimization, spot instances, auto-scaling (no notes yet)
- [ ] Understand model registries and artifact management (no notes yet)

---

## ML Fundamentals

- [ ] Review training pipeline: loss, optimizers, regularization ([notes](../topics/ml_fundamentals/deep_learning.md))
- [ ] Review evaluation metrics: precision, recall, F1, AUC-ROC ([notes](../topics/ml_fundamentals/deep_learning.md))
- [ ] Review classical ML algorithms ([notes](../topics/ml_fundamentals/classical_ml.md))
- [ ] Review neural network architectures ([notes](../topics/ml_fundamentals/neural_networks.md))
- [ ] Study RLHF, DPO, and alignment for LLMs (no notes yet)
- [ ] Review LLM merging techniques ([notes](../topics/ml_fundamentals/llm_merging.md))

---

## Behavioral

- [ ] Prepare story about building/deploying an ML system end-to-end
- [ ] Prepare story about debugging a production ML issue
- [ ] Prepare story about collaborating across teams (data science + engineering)
- [ ] Prepare story about making tradeoffs under constraints (cost, latency, quality)
- [ ] Practice "Why Bumble?" answer — connect to mission, product, ML challenges
- [ ] Practice "Tell me about yourself" (2-min version)
