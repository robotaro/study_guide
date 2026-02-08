#llm #rag #retrieval #embeddings

# Retrieval-Augmented Generation (RAG)

## 1. Overview

RAG augments an LLM with external knowledge by retrieving relevant documents at inference time and including them in the prompt. This addresses core LLM limitations:

- **Knowledge cutoff**: LLM knowledge is frozen at training time
- **Hallucination**: model generates plausible but incorrect facts
- **Domain specificity**: pre-trained models lack specialised/proprietary knowledge
- **Verifiability**: retrieved sources can be cited, making outputs auditable

> **Source**: Lewis et al., "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks" (2020)

## 2. Architecture

```
User Query → [Embedding Model] → Query Vector
                                      ↓
                              [Vector Database] → Top-k relevant chunks
                                      ↓
                        [Prompt Construction] → "Given context: {chunks}. Answer: {query}"
                                      ↓
                                   [LLM] → Response (grounded in retrieved context)
```

### Core Components
1. **Document ingestion**: load, chunk, embed, and store documents
2. **Retrieval**: given a query, find the most relevant chunks
3. **Generation**: feed retrieved context + query to the LLM

## 3. Document Chunking

Documents must be split into chunks before embedding. Chunking strategy significantly impacts retrieval quality.

| Strategy | How | Best for |
|:--------:|:---:|:--------:|
| Fixed-size | Split every $n$ tokens/characters with overlap | Simple, general purpose |
| Sentence-based | Split on sentence boundaries | Preserving semantic units |
| Recursive | Split by paragraphs → sentences → tokens (hierarchical) | Structured documents |
| Semantic | Split when embedding similarity drops between consecutive sentences | Variable-structure text |
| Document-aware | Use headings, sections, page breaks | PDFs, HTML, markdown |

### Key Parameters
- **Chunk size**: typically 256-1024 tokens. Smaller = more precise retrieval, larger = more context per chunk
- **Chunk overlap**: typically 10-20% of chunk size. Prevents splitting relevant information across chunks

## 4. Embedding Models

Convert text into dense vectors that capture semantic meaning. Similar texts have similar vectors.

### Key Properties
- Output dimension: typically 384-1536 dimensions
- Trained on large-scale contrastive learning (positive pairs = semantically similar texts)
- Asymmetric models: separate encoding for queries (short) and documents (long)

### Common Models

| Model | Dimensions | Context | Notes |
|:-----:|:----------:|:-------:|:-----:|
| OpenAI text-embedding-3-small | 1536 | 8191 | Good quality, API-based |
| BGE (BAAI) | 768-1024 | 512-8192 | Open-source, strong multilingual |
| E5 (Microsoft) | 768-1024 | 512 | Instruction-tuned for different tasks |
| GTE (Alibaba) | 768 | 8192 | Open-source, long context |
| Cohere embed-v3 | 1024 | 512 | API-based, supports compression |

### Similarity Metrics

$$
\text{Cosine similarity} = \frac{q \cdot d}{||q|| \cdot ||d||}
$$

- Cosine similarity is the standard for normalised embeddings
- Dot product when embeddings encode magnitude as relevance
- Most embedding models are trained with cosine similarity

## 5. Vector Databases

Store and index embeddings for fast approximate nearest neighbour (ANN) search.

| Database | Type | Key feature |
|:--------:|:----:|:-----------:|
| FAISS (Meta) | Library | Fast, supports GPU, multiple index types |
| Pinecone | Managed service | Serverless, filtered search |
| Weaviate | Self-hosted / cloud | Hybrid search (vector + keyword) |
| Chroma | Lightweight | Easy to use, good for prototyping |
| pgvector | PostgreSQL extension | Integrates with existing Postgres |
| Qdrant | Self-hosted / cloud | Filtering, payload storage |

### Index Types (FAISS)
- **Flat (brute force)**: exact search, $O(n)$. Best quality, slow for large datasets
- **IVF (Inverted File Index)**: clusters vectors, searches only nearby clusters. Trade-off: accuracy vs speed
- **HNSW (Hierarchical Navigable Small World)**: graph-based, excellent recall at high speed. Most popular for production

## 6. Retrieval Strategies

### 6.1 Dense Retrieval (Semantic)
- Embed query and documents into the same vector space
- Find nearest neighbours by vector similarity
- Captures semantic meaning ("car" matches "automobile")
- Weakness: can miss exact keyword matches

### 6.2 Sparse Retrieval (Keyword)
- Traditional methods: BM25, TF-IDF
- Matches based on exact/fuzzy token overlap
- Fast, interpretable, works well for specific terms and proper nouns
- Weakness: misses semantic similarity

### 6.3 Hybrid Retrieval
Combine dense and sparse retrieval, then fuse the results:

$$
\text{score}_{hybrid} = \alpha \cdot \text{score}_{dense} + (1 - \alpha) \cdot \text{score}_{sparse}
$$

- Reciprocal Rank Fusion (RRF) is a common rank-based alternative to score fusion
- Typically outperforms either method alone
- Most production systems use hybrid retrieval

### 6.4 Reranking
After initial retrieval (top-50 or top-100), apply a more expensive cross-encoder model to rerank:
1. Retrieve top-$k$ candidates with a fast bi-encoder
2. Score each (query, document) pair with a cross-encoder
3. Return the top-$n$ after reranking ($n < k$)

- Cross-encoders are more accurate than bi-encoders because they see query and document jointly
- But they're too slow for initial retrieval (must score every document)
- Two-stage pipeline: fast retrieval → accurate reranking

> **Source**: Nogueira & Cho, "Passage Re-ranking with BERT" (2019)

## 7. Advanced RAG Patterns

### Query Transformation
- **Query expansion**: rephrase or expand the query to improve recall (e.g., generate multiple query variants with the LLM)
- **HyDE (Hypothetical Document Embeddings)**: ask the LLM to generate a hypothetical answer, then embed that answer for retrieval. The hypothesis is closer in embedding space to the real answer than the question is

> **Source**: Gao et al., "Precise Zero-Shot Dense Retrieval without Relevance Labels" (HyDE, 2022)

### Multi-step Retrieval
- Retrieve → read → generate follow-up queries → retrieve again
- Useful for complex questions requiring information from multiple sources

### Contextual Compression
- After retrieval, extract only the relevant sentences/passages from each chunk
- Reduces context length and noise fed to the LLM

## 8. Evaluation

| Metric | What it measures | Level |
|:------:|:----------------:|:-----:|
| Recall@k | Fraction of relevant docs in top-k retrieved | Retrieval |
| MRR (Mean Reciprocal Rank) | How high the first relevant result ranks | Retrieval |
| NDCG | Graded relevance (not just binary) with position discount | Retrieval |
| Faithfulness | Is the answer supported by the retrieved context? | Generation |
| Answer relevance | Does the answer actually address the question? | Generation |
| Context relevance | Are the retrieved chunks relevant to the question? | Retrieval |

**Frameworks**: RAGAS and TruLens provide automated RAG evaluation pipelines using LLM-as-judge for faithfulness and relevance.

## 9. Common Interview Questions

- **Q: When would you use RAG vs fine-tuning?** RAG for up-to-date or frequently changing knowledge, when you need source citations, and for proprietary data you don't want baked into model weights. Fine-tuning for changing the model's style/behaviour, learning new tasks, or when retrieval latency is unacceptable.

- **Q: How do you handle the case where no relevant documents are retrieved?** Set a similarity threshold — if no results exceed it, have the model say "I don't have enough information" rather than hallucinate. Also consider a fallback to the model's parametric knowledge with appropriate caveats.

- **Q: What's the difference between bi-encoder and cross-encoder retrieval?** Bi-encoder embeds query and document independently, enabling fast ANN search but missing fine-grained interactions. Cross-encoder processes query and document jointly (concatenated), capturing token-level interactions but requiring O(n) scoring. Use bi-encoder for retrieval, cross-encoder for reranking.

- **Q: How do you evaluate a RAG system end-to-end?** Separately evaluate retrieval quality (recall@k, MRR) and generation quality (faithfulness, relevance). Use LLM-as-judge for scalable evaluation. Track failure modes: retrieval misses, context ignored, hallucination despite good context. A/B test with real users for production systems.
