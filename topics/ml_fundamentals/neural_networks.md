# Famouns Neural Networks

## 1. You Only Look Once (Yolo)

### 1.1 Introduction
* State of the art object detection and tracking algorithm
* Created in 2015 and outperformed R-CNN, Fast R-CNN and Faster R-CNN
* Souces
[https://www.youtube.com/watch?v=ag3DLKsl2vk](Codebasis)
[https://www.youtube.com/watch?v=IfRMV2MY9n0](Codebasis)

### 1.2 Algorithm

Simple version
1. Divide the original image into cells (like 4x4, 20x20, etc)
2. Each create label vector with a probabity of there being an object, the bounding box of said object, and the probabilities of all known classes. So for 2 classes, the vector is size 7 (1 + 4 + 2). The bouding box values are normalised and relative to the cell itself 
3. Train the network and that will output an image with labels for each of the cells, and labels with object will also predict where the bouding boxes are

Problem with the simple version:
* Multiple cells have proposed different bounding boxes:
    * You pick the area using Non-Max Supressiong (NMS)
* What happens if there are multiple centre of objects in one cell?
    * You concatenate the vector of both classes, so from our previous example, it would be a size 14 vector (7 + 7)
    * You network will change to accomodate to accept the maximum number of objecter per cell.

### 1.3 Versions
* YOLO v1 (2016)
* YOLO v2 (2017)
* YOLO v3 (2018)
* YOLO v4 (2020)
    * Uses the following architectures for the "neck"
        - Feature Pyramid Network (FPN)
        - Path Agregagtion Network (PAN)
        - (RFB)

## 2. MobileNet

### 2.1 Introduction
* Designed to fit small micronctrollers and other embedded hardware (EDGE).
* Much smaller than normal networks

### 2.2 DepthWise Separate convoltion (Super speed-up trick for CNN)
* This is the trick that allows us to reduce the number of total computations for a convolution
* This method is much faster because the number of multplications differs with the square of the kernel size. For a 1024 filerts with size 3, depthsize convoltion only requires ~10% the number of operations compared to a standard convolution
* It also has far fewer parameters!

#### 2.2.1 Steps
1. Depthwise Convolution: Filtering stage
    * Applies a convoltion to a single channel at a time. So if the image is of depth is 3 (RGB) it would use create one "subfilter" per channel with depth 1 each.
2. Pointwise Convolution: Combination Stage
    * Now that you performed you convoltion on each channel separetly, you put it all together with a 1x1 kernel convolution on the output from the previous stage in order to combine the information

## XCeption
* A new network that is pushing the depthwise separate convoltions as the new standard for DNN
* Faster to train than Inception v3, also higher accuracy for the same number of epochs compared to Inception v3
* 
