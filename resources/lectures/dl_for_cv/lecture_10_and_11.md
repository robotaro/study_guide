# Training Neural Networks 1 (Lecture 10)

## 1. One Time Setup
### 1.1 Activation functions
* **Sigmoid**
    * With only sigmoid functions, the gradients can be positive and negative as you go up the network, so you get a jittering gradient around the ideal gradient for training
    * On a GPU, it takes longer to move memory than computing the actual non-linear function itself. Sigmoid is slower than ReLU, but you probably won't see this effect when running it on the GPU, only on CPUs
    * (Problem) Not zero-centered!
    * (Problem) Vanishing gradient problem!
* **Tanh**
    * It is zero-centered
    * Still has vanishn gradients problem
* **Relu**
    * Very fast to comput
    * Mitigates the vanishing gradients problem
    * Converges faster than sigmoid or Tanh
    * (problem) not zero-centered output
    * (problem) What happens with x < 0? The gradient is zero, and that neuron is "dead". It will never activate or update. Once this ReLU gets knocked out of the data cloud, it never comes back.
* **Leaky-ReLU**
    * No vanishing gradients / doesn't saturate
    * Also very fast
    * Also converges very fast
    * It will not "die"
    \[f(x)=max(0.01x,x)\]
* **Parametric Rectifier (PReLU)**
    * now you have an alpha as a parameter for the negative slope 
    \[f(x)=max(\alpha x,x)\]
    * The problem is that alpha is yet another parameter to be learned
* **Exponential Linear Unit (ELU)**
    * All benefite from ReLU
    * Negative saturation regime compare to leaky ReLU
    * Exp() is back, and it also has a new alpha
* **Scaled Exponential Linear Unit (SELU)**
    * Promissing new idea

### 1.2 Preprocessing
* **Z-Scale** is a very common practice (minus mean, and dividing by std)
* **Decorrelation** Using PCA
* **Whitening** the data means trasforming a set of random variables of know covariance matrix into a set of new variables with an idendity covariance matrix, I.E, all vectors are now uncorrelated/orthogonal to each other. NOT COMMON for image data
* **NOTES** Parameters for pre-processing are done on the training data, and applied on the test data

### 1.3 Weight Initialisation

* Random weights (from a Gaussian distribution) are okay for shallow networks, but not for deeper ones. 
* As you go deeper, the  gradients  from a distribution std=0.01 tend to become even smaller gather around zero, and if they are zero there is no learning.
* for **Tahn**, you want to use **Xavier** intitialisation, where the std=sqrt(1/num_dimensions)!
* for ReLU , you wan to to use **Kaiming / MSRA Initialisation**, where std=sqrt(2/num_dimensions). This is enough to get VGG to train from scratch :)

### 1.4 Regularisation
* You regularise your loss to prevent overfitting, like adding a L2 (most common) or L1 loss.
* Or you can also use **Dropout** to remove some neurons, and the common amount is 50% per layer. This has the effect of preventing the network from having a redundant representation of features
* Dropout is used during training, and the mask can change throughout the training, so that makes the model non-deterministic! So what we want to do is to calculate the average expectation of each dropout layer
* **Batch Normalisation** to has replaced dropout!
* GO BACK TO THE SLIDES LATER! THERE IS MORE TO LEARN!

### 1.5 Data Augumentation
* You want to flip the image, rotate, scale, sheear, change saturation, do a random crop etc, in order to add variability in your data

#### 1.5.1 DropConnect
* Random weights

#### 1.5.2 Fractional Pooling
* Different neurals will have diffent receptive field-sizes

#### 1.5.3 Stochastic Depth
* Training: Skip some residual blocks in Resnet
* Test: Use all blocks

#### 1.5.4 Cutout
* Remove parts of the image by just filling them with zeros.

#### 1.5.5 Mixup
* You can mix images using a Beta distribution for the contribution values. It does work!

### Most common:
* Batch Normalisation
* Data Augumentation
* Cutout
* Mixup

## 2. Training Dynamics (Lecture 11)

### 2.1 Learning Rate Schedules
First get things to work, and THEN try to tweak the learning rate by using schedules
* **Constant** THE MOST POPULAR! People just select a number and leave it.
* **Step**: Start with a large learning rate and go smaller and smaller every N number of epochs
* **Cosine** Reduce rate at a few fixed points using a cosine function. THis has become very popular in the recent years. All you need to do is wait
\[\alpha_{t}=\frac{1}{2}(1+cos(t\pi/T))\]
* **Linear** The learning rate decays linearnly over time, nothing crazy. Works very well too.
* **Inverse Square** duh

### 2.2 Early Stopping
* Create checkpoints as you train and once you stop training, observe the train and validataion losses and pick a point in time where the model is optimal (where they are the closest), and use that model

### 2.3 Choosing Hyperparameters
You need to pick the weight decay (coefficient for your regularisation) and the learning rate
* **Random** For NN it is toooo expensive
* **Grid Search** For NN it is toooo expensive
* Good practices (CHECK SLODES FOR ALL NOTES)
    * Step 1) Check Initial loss
    * Step 2) Overfit a small sample
    * Step 3) Find Learning Rate that makes loss go down
    * Step 4) Coarse grid, train for ~1-5 epochs
    * Step 5) Redefine grid, train for longer
    * Step 6) Look at the learning curves

### 2.4 Diagnose Training
* Check the slides on how to read the curves

## 3. After Training

### 3.1 Model Ensembles
* You can traing N models, under different conditions, and use ALL of them during your test time. Ypu get a prediction from each of them and you average their classification into your final label. This tends to add about 2% of extra performance
* You can also use different checkpoints as your multiple models for your ensenble. Nice!
* **Polyak averaging**, you keep a expontential running averages of the weights in the network as different models to help smooth the noise from the consensus.

### 3.2 Transfer Learning
This is the NORM in the compute vision community!
People say you need very, very large datasets to train a CNN. But you don't! You can just re-train the last layer of the netural network!
1) Train your network
2) Remove last layer and freeze all previous layers
3) Add a new last layer and continue training updating the whole model! Or Slap your own classifier at the very end and use the network as sophisticated feature representation :)

| | Dataset similar to ImageNet | Dataset very different from ImageNet|
| :-----: | :------: |:--------:|
| Very litte data (10s to 100s)| Use a linear classifier on top layer| you're in trouble...|
| A lot of data (100s to 1000s)|Finetune a few layers   | Finetue a larger number of layers        |

