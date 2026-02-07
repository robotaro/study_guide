# Probabilities

## Good Sources
* **Bayesian Inference** [https://statswithr.github.io/book/bayesian-inference.html]()




## Vocabulary

* **Expected value** : The weighted average value from a distribution. This is what they are refering to! It is also refered via the "^" (hat) on top of a variable
* **p-value** : 

* 1. **Sigmoid Function**: This function, also known as the logistic function, maps any real number to the (0, 1) interval, making it suitable for transforming logits (log-odds) into probabilities. The formula for the sigmoid function is:
    
    𝜎(𝑥)=11+𝑒−𝑥σ(x)=1+e−x1​
    
    Here, 𝑥x is a real number (such as the output from a linear combination of input features in logistic regression), and 𝜎(𝑥)σ(x) provides a probability output between 0 and 1.
    
2. **Logit Function**: Conversely, the logit function takes a probability (a number between 0 and 1) and maps it to a real number ranging from negative infinity to positive infinity. The formula for the logit function is:
    
    logit(𝑝)=log⁡(𝑝1−𝑝)logit(p)=log(1−pp​)
    
    where 𝑝p is the probability value.
3. **Expectation**: The expectation (expected value) of an arbitrary deterministic expression over a random vector:
	1.- **Calculation:** To calculate the expectation, you multiply each possible value of the random variable by its probability and then sum up those products. 
- **Notation:** The expected value of a random variable X is often denoted as E(X) or μ.
![[Pasted image 20250122223522.png]]