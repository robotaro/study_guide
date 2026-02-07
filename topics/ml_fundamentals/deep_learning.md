# 1. Machine Learning

## 1.1 Knowledge Distilation
Ways to compress a neural network
- Student / Teacher training
- Prunning

## 1.2 Important Trends To Keep in Mind

### 1.2.1 On Training Practices
- Always separate your data into *training*, *valindation* and *test* sets. You train on the training data, choose your your hyper parameters on the validation data and evaluate your final performance on the test set. **NEVER** adjust your parameters in any shape or form using the test data! 
- Of course, you can extend the previous idea by using cross-validation, where you split the training data into chunks and cycle through choosing one as the validation set. Common names: 5-Fold Cross Validation

### 1.2.2 Concepts
- **Curse of dimensionality** : For uniform coverage of space, the number of training points needed grows exponentioaly with the number of dimensions 
- **Hebbian Learning** - Fire together, wire together
 - **Condition Number** - The ration of the larges to the smallest singular value of the hessian matrix. This causes the zig-zag effect on the loss value when one loss function is much narrower on one dimension

### 1.2.3 KL-Divergence and Cross Entropy
- Kullback-Leiber Divergence (KL-Divergence) : It works out to be the -log of the probability assigned to the correct class
\[D_{KL}=\left ( P||Q \right ) = \sum_{y}P(y)log{\frac{P(y)}{Q(y)}}\]
- Entroy
\[H(P)=-\sum^{n}_{i=1}P(x_{i}) log P(x_{i})\]
- Cross Entropy
\[H(P,Q)=H(p)+D_{KL}(P||Q)\]

### 1.2.4 On Classifying Image using Linear Classifiers
- Any image, before being classified, becomes a 1D vector. This destroyes any spatial information by that's how the linear classifiers require its input data vector

### 1.2.5 Relevant videos
    https://www.youtube.com/watch?v=gZPUGje1PCI

    How to Use TensorFlowLite on a actual project
    https://www.youtube.com/watch?v=iTj0lcVSIVU

### 1.2.6 Vocabulary
- **logits** : In Math, Logit is a function that maps ([0, 1]) to R ((-inf, inf)). The logit of the probability is the log of the odds:

- **Identity Function** : A function that Y = X, or F(X) = X. Output equals input

$$
logit(p)=ln\frac{p}{1-p}
$$

##1.2 Norms (Distance Metrics)
- L1 Norm : Manhattan Distance, the sum of the absolute values of a vector
$$
d_{1}(I_{1},I_{2}) = \sum_{}^{p} \left| I_{1}^{1} - I_{p}^{2}\right|
$$

- L2 Norm : Euclidian Distance, the sum of the squares of the values of a vector
\[d_{2}(I_{1},I_{2}) = \sqrt{\sum_{}^{p} \left( I_{p}^{1} - I_{p}^{2}\right)^{2}}\]
* **receptive Field** : Generaly speaking, it is the region of th einput space that effects a particualr unit of the network. For filters, it the actual area covered by the filter during a step of the convolution.

## 1.3 Loss-Functions
* Multiclass SVM Loss : Simple score that tells us which class is better
* Cross-Entropy (Multinomial Logistic Regression) : Interprets classifier scores as probabilities. This gives us a probability distribution over the available class. You get the probabilities via the softmax function at the end


## 1.4 Regularisation
It's a term added to the final value of the loss function to prevent the mode from doing too well. This allows us to genenaralise beyone the training data.

Here you have the Complete loss function equal to the average loss of the function with data x, weights W and labels y, and a regularisation term of the weights with coefficient lambda:

\[L(W)=\frac{1}{N}\sum_{N}^{i=1}L_{i}\left ( f\left ( x_{i}, W\right ),y_{i} \right ) + \lambda R(W)\]
* Simple methods 
    * L1 : (Manhattan) Prefers to put all your weight on a single feature 
    * L2 : (Euclidian) Prefers to spread the weights across all features
    * Elastic Nets (L1 + L2)
* Advanced methods
    * Dropout
    * Batch Normalisation
    * Cutout
    * Mixup
    * Stochastic depth
    * etc

## 1.5 Optimisers
It's a process to adjust the weights of our model in order to minimise the loss. The full loss contains the data loss and the regularisation loss

### 1.5.1 Quick recap
- SoftMax function : $$

L_{i}=-log(\frac{e^{y_{i}}}{\sum_{j}e^{s}j})
$$
* Complete Loss Function (with Regularisation of weights):  
$$
L=\frac{1}{N}\sum^{N}_{i=1}L_{i}+R(W)
$$

### 1.5.2 Calculating gradients
* Numeric Gradient (Finite differences) : Aproximate, slow, easy to write
* Analytic Gradient : Exact, fast, error-prone

*In practie*: Always use analytic gradient, but check implemetation with numerical gradient. This is called **gradient check**

In order to calculate gradients you have to look through all training samples, and if that is very large, each step would take too long. So thre are waits of reducing the need to go over of them

\[L_{i}=-log(\frac{e^{y_{i}}}{\sum_{j}e^{s}j})\]

* Complete Loss Function (with Regularisation of weights):  \[L=\frac{1}{N}\sum^{N}_{i=1}L_{i}+R(W)\]

* Batch Gradient Descent : 
\[L(W)=\frac{1}{N}\sum^{N}_{i=1}L_{i}(x_{i}, y_{i}, W)+\lambda R(W)\]

#### 1.5.2.1 Stochastic Gradient Descent (SGD): 
\[\nabla_{W}L(W)=\frac{1}{N}\sum^{N}_{i=1}\nabla_{W}L_{i}(x_{i}, y_{i}, W)+\lambda\nabla_{W}R(W)\]
Think of loss as an expectation over the full distribution p_data.This is an average of the independent loss function of the samples over our data set. This full expectation over all the data now becomes a Monte-Carlo Estimation. We estimate the gradient using this monte-carlo estimation.

\[L(W)=\mathbb{E}_{(x,y)~p_{data}}[P(x,y,W)] + \lambda R(W) \approx \frac{1}{N}\sum^{N}_{i=1}L_{i}(x_{i}, y_{i}, W)+\lambda R(W)\]

\[\nabla_{W}L(W)=\nabla_{W}\mathbb{E}_{(x,y)~p_{data}}[P(x,y,W)] + \lambda \nabla_{W} R(W) \approx \frac{1}{N}\nabla_{W}\sum^{N}_{i=1}L_{i}(x_{i}, y_{i}, W)+\nabla_{W}R(W)\]

* Hyperparameters:
    * Weight Initialisation
    * Number of steps
    * Learning Rate
    * Batch Size
    * Data Sampling

* Limitations
    * Learning rate (step size) is the same on all dimensions, so it causes jitter along the steep dimension, and very slow progress along the shallow dimension. This is also known as the "High Condition Number"
    * Local minimum / Saddle points : This becomes more common in high dimensions, where you may have values of the objective function (loss function) increasing in one dimention and decreasing in another dimensions
    * Gradients come from minibatches so they can be noisy! This would make the direction of optimisatoin to wonder instead of going for the minimum value directly. To overcome this, we use "momentum". Rho is usually 0.9 or 0.99
    * Neterov Momentum : You "look ahead" to the point where  updating using velocity would take us; ask "what would the gradient have been if I had taken that step witht that velocity"

        | SGD             | SGD + Momentum |    Nesterov Momentum   |
        | :-------------: |:-------------: |:-------------: |  
        |\[x_{t+1}=x_{t}-\alpha\nabla f(x_{t})\]| \[v_{t+1}=x_{t}-\rho v_{y} + \nabla f(x_{t})\]\[x_{t+1}=x_{t}-\alpha v_{t+1}\]  | \[v_{t+1}=\rho v_{t}-\alpha\nabla f(x_{t} + \rho v_{t})\]\[x_{t+1}=x_{t}+v_{t+1}\]

 * AdaGrad (Adaptive Gradient) : You keep a historical sum of squares of gradients for each dimension. "Per-Parameter learning rates" or "adaptive learning rates"
    * Limitations:
        * As you keep adding squares, you tend to build large number to divide your step by, so that will halt the process. So you want to add a "decay rate", like friction, to prevent it from growing so quickly.
    * Solution: **RMSProp** a leaky version of AdaGrad 

* Adam (RMSProp + Momentum): Combine the two good ideas! You keep track of the first moment (the velocity) and the "leaky" exponention average of square gradients. 
    * One problem is the very first step, that would have a very small momentum 
```python
# Adam
moment1 = 0
moment2 = 0
beta1 = .9
beta2 = .999
learning_rate = 1E-3
for t in range(num_steps):
    dw = compute_gradient(w)
    moment1 = beta1 * moment1 + (1 - beta1) * dw
    moment2 = beta2 * moment2 + (1 - beta2) * dw * dw
    moment1_unbias = moment1 / (1 - beta1 ** t)  # To avoid taking a very large first step
    moment2_unbias = moment2 / (1 - beta2 ** t) # To avoid taking a very large first step
    w -= learning_rate * moment1_unbias / (np.sqrt(moment2_unbias) + 1E-7)
```

### 1.6 Summary on First Order Optimisation Algorithms
 | Algorithm | Tracks first moments (Momentum) |    Tracks second moments (Adaptive Learning rate) | Leaky second moments | Bias correction for moment estimates 
| :-------------: |:-------------: |:-------------: |:-------------: |:-------------: |  
|SGD|no|no|no|no|
|SGC + Momentum|**Yes**|no|no|no|
|Nesterov|**Yes**|no|no|no|
|AdaGrad|**Yes**|**Yes**|no|no|
|RMSProp|**Yes**|**Yes**|**Yes**|no|
|Adam|**Yes**|**Yes**|**Yes**|**Yes**|

**Notes on First-Order Optimisation:**
SGD+Momentum may outperform Adam, but it may require more tuning. Go for Adam first and try out after you get it to work

**Notes on Second-Order Optimisation:**
 Hessian matrix has O(N^2)
 Inverting the Hessian matrix is O(N^3) !!! Impractical :(

Note:  They use an Explicit Euler Integration method here, instead of a Verlet Integration, or Runge-Kutta Method

## Maximum Likelyhood Estimation
* TODO