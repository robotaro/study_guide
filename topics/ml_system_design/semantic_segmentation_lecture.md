# Semantic Segmentation

Link [https://www.youtube.com/watch?v=XMSjOatyH0k&list=PLog3nOPCjKBnpklR-RHZGWXbzjBkKI7cI&index=10]

## 1. Fully Convolutional neural networks
* They work on any size image, all it does is to change the size of the activation maps internally.
* These are used to provide a score for each pixel in the final FC layer, I.E. pixelwise prediction
* To create these networks you:
    * Replace the FC layers with 1x1 convolutional layers. This allows you to use all previously trained CNN layers as a backbone.
        * A 1x1 conv keeps the same size of the activation map, and only scales the values.
        * A 1x1 conv with 16 depth is like 14 neurons connected to all th activation map
        * You can also use 1x1 conv layers to reduce the depth of an input tensor, for ample, if it is 200x32x32, you can pass it via 32 filters of 1x1x200 and you get an activation map of 32x32x32
    * Make sure that the last layer is the size of the original image.
* These networks also predict a mask where it is upsampled
    * The upsampled prediction at the end is often created by combining the upsapling form different internal levels. You take the outpt form one salyer and you upsample it and oyu can see  how they become finer the deeper tou take them from
* **SDS (Simultaneous Detection and Segmentation)** is a R-CNN-based method that uses object proposals

## 2. Autoencoder style architectures
* **SegNet**
    * I've seen this as well in the previou network, it compresses and decompresses the data just like a autoencoder but with segmentation instead
    * **Unpooling** : You keep the pooling indices from one end to another, and fill the empty pixes in the activation maps with:
        * Interpolation (old method)
        * Series from convolutional layers
        * **DeconvNet** : We learn the deconv filter to use on the known pixels at their original position
* **U-Net**
    * Also an encoder-decoder architectured with skip connections. So you connec the output from a layer from one side to its respective counter part on the other side. The rest of the network is still an auto encoder. The image looks like a "U", hence the name
    * This architecture is populat because it you can get the high-level info form the bottleneck of the autoencoder, and low level from the counter-part of each level coming form the encoder network
* **DeepLab**
    * One of the most famous and well-performing architecture in semantic segmentation
    * It takes 3 key areas:
        * Reduced feature resoltion : Proposed solution by Atrous convolutions
        * Objects exists at multiple scales : Proposed solution by Pyramid pool, as in detection
        * Poor locationsation of edges : Proposed solution by refinement with conditional random field (CRF)
    * **Dilated (atrous) Convolutions**
        * if you want to get a feature map of same size of the origina feature you can first downsample the image to a smaller resoltion (strid 2 for example), then you do the convolution using a 7x7 kernel, and then you upsample back (same stride 2). The problem is that it gives you a sparse feature map back.
        * To solve this we use dilated convolutions, so the kernel is sparsed. You move across all with stride 1, but where you sample the pixels is sparsed, which gives you a dense output feature map. (check her slides)
        * 