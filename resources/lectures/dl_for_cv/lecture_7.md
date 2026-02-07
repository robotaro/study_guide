# 7. Convolutional Networks

## 7.1 Convolution Layers
* A set of many filters (3x5x5 for example) is used on the image at first.
* The resulting image from the output from the filters is called an **activation map**. It shows which parts of the original image are more sensitive to the filter used. One activation map per filter.
* There is **one** bias term per convolutional filter. 6 filters means a vector with 6 bias values
* The generica way to represent input images is with a 4 dimensional tensor, (num_images, color_channels, width, height)
* Don't forget to add an activation function after the convolution operation!
* **Padding** is used prevent the image from shrinking from layer to layer. Zero-padding is a common solution, but you can copy other values, etc.
* The output from a filter into the activation map is called the **receptive field**
* When working with large images, like 1024x1024, you often downsize the image in the network itself
* In order to downsize image, you can use a **stride** value that will make the filter skip a few positions during convolution. Stride of 1 is the normal step. Stride of 2 skips the next step, and so on.
* You can find a 1x1 convolution layer in a CNN, a "network in a network" and that is a fully connection network that operates on the feature vector created by all filters in the previous layer. It operates independently in each of the feature vectors that appear in each of the positions in space.

### Hyperparameters
The number if channels in one layer equals to the number of filters in the previous, don't forget
* Kernel Size \[K_{H} \times K+{W}\]
* Number o Filters \[C_{out}\]
* Stride (How far the filter moves in the covolution. You can use this to) \[P\]
* Padding ( for a 3x3 pad with 1, for a 5x5 filter pad with 2 - cos a 5x5 removes two pixels, got it?) \[S\]

#### Weight Matrix
\[C_{out} \times C_{in} \times K_{H} \times K_{W}\]

#### Bias Vector
\[C_{out} \]

#### Output Size
\[C_{out} \times H' \times W'\]
where
\[H' = (H-K+2P)/S + 1\] \[W' = (W-K+2P)/S + 1\]

## 7.2 Pooling Layers
* Designed to downsample inside the network without adding more learnable parameters
* You can get the the highest value within a region (or the mean), to pass on to the downsampled region in the next layer.
* It's common to use a stride 2 size 2x2, so that the regions don't overlap
* This introduce invariance to small partial shifts with no learnable parameters! Makes sense, as even if the edge moves withing the pooling rerion, you are still getting its maxium value across.

## 7.3 Classical architectures
* [Conv, ReLu, Pool] x N, flatten [FC, Relu] x N, FC, example:
    * LeNet-5

## 7.4 Batch Normalisation
* Batch normalisation normalises the output from a previous layer so that it has zero mean and unit variance distribution.
* They make your network train A LOT faster
* The original paper explains that, as the weights of one layer change over the course of training, so will the distribution of values of its output and by extension the next layer. Normalisation grounds these changes.
* By the way, this is effectivally taking the z-scalling of the weights, and this operation is differentiable
* Batch normalisation is a linear operation that can be fused with a previous linear operation at test time. Nice!
* They are usually inserted after fully connected (FC) layers, and before non-linearity
* **Internal Covariate Shift** is not yet really well understood up to this day. I can make your mode behave different between training and testing.
* **Layer Normalisation** normalises over the features instead of batch dimension
