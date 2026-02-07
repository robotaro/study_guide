# Lecture 5 : Neural Networks

## Notes on Feature Representation
* When using non-linear feature transforms, a linear classifier in feature space is a non-linear classifier in original space :) 
* A common feature representation is using only the color histogram of an image. You through away the spatial information and only focus on the colors. This could be useful in certain cases.
* "Distributed representation" is the act of represengin one image using a combination of different image templates

## Historical features
* **Histogram-of-Oriented-Gradients (HoG)** (popular in 90's)
    1. Compute edge direction / strength at each pixel
    2. Divide image into 8x8 regions
    3. Within each region, comput histogram of edge directions weighted by edge strength
* **Bag of Words** (popular in 00's and early 10's)
    1. Build codebook: 
        * Extract random patches, of different sizes
        * Cluster patches to form "codebook" of "visual words"
    2. Encode Images
        * Build a histogram of features for each image using your codebook
        * You can use different codebooks for the same image to get a better representation.

## Adding layers
Bias terms are ommited for clarity! Also, ReLU is added here for free (REctified Linear Unit)
* (Before) Linear score function:
\[f=W_{x}\]
* (Now) 
    * 2-Layer Neural network: \[f=W_{2}max(0,W_{1}x)\]
    * 3-Layer Neural network: \[f=W_{3}max(W_{2}max(0,W_{1}x))\]
    * etc
* Notes: 
    * You count the layers as the weight matrices
    * Without an activation function, you could combine all weight matrices into one, and you're back at a linear classifier

## Space Warping
* Don't regularise by changing the size of your hidden layers, instead use stronger L2 loss

## Universal Aproximation
* A NN with one hidden layer can approximate any continuous function f: R^N -> R^M with arbitraty precision (function must be continuous, only works on compact subsets sof R^N)

# Convex function
* Mathematical defition says that no values between points A and B on a function are greater thant the secant between these values (check slides)
* Generatly speaking, convex functions are easy to optimise: You can derive a thoretical guaranteers about coverging toa global minimum
* In practice, convex optimisatoin problems are quite easy to solve, and they don't depend on initialisation
* Most NN work on optimising non-convex loss functions
