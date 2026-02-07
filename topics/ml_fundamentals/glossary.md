#glossary

- Probability: an apple
- Sigmoid Function
- *Logit Function:* The logit function is the logarithm (typically natural logarithm) of the odds ratio. Mathematically, it's expressed as:$$
\text{logit}(p) = \log\left(\frac{p}{1-p}\right)
$$
It is the Inverse of the sigmoid function!
- Cosine Similarity: Used to measure the similarity between two multi-dimensional vectors, very common in comparing similarity between token vectors in NLP

## LLM
1. **Basic Quantization (Uniform)**:
    
    - **Advantages**: Simpler to implement, faster to compute.
    - **Disadvantages**: May not capture the distribution of weights well, potentially leading to larger quantization errors and reduced model accuracy.
    - **Use Cases**: Suitable for scenarios where implementation simplicity and speed are critical, and slight loss in accuracy is acceptable.
2. **K-means Clustering Quantization (Non-uniform)**:
    
    - **Advantages**: Typically results in better model accuracy because it adapts to the actual distribution of the weights, reducing quantization error.
    - **Disadvantages**: More complex and computationally intensive to implement, as it requires running the k-means clustering algorithm.
    - **Use Cases**: Preferred when maintaining higher model accuracy is essential, and the overhead of more complex quantization is acceptable.