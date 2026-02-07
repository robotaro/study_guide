# Lecture 16 : Detection and Segmentation

## 1. "Slow" R-CNN Training
* Predicted boxes are labelled as positive, negative and neutral (when they are a subbox)
* One of the goals is to transform the raw bounding box parameters into a target output bouding box.
* Bouding boxes are paired-up before training, so you know what label that bouding box should belong to
* R-CNN is not inventing bounding boxes from scratch, it learn how to perturn/refind the input proposed regions

## 2. Faster R-CNN Training
* Stage 1) Pregion proposal netowrk convert the fixed set of anchor boxes into a set of region proposals
* Stage 2) Transform region proposals in to final object boxes

* You work on classifying the region for the anchor boxes
* The region proposals coming out of each image will change over the course of training because we're training both stages at the same time
* The fact that we snap the bouding boxes during training creates two problems
    1. Misaligned Features due to snapping
    2. Can't backprop the box coordinates
* One way around that is using bilinear interpolation to sample in between the pixels of features

## 3. Sumary of objecct detection methods
* Slow R-CNN: Run CNN independently for each region
* Fast R-CNN: Apply differentiable cropping to shared image features
* Faster R-CNN: COmputer proposals with CNN
* Single Stage (SSD): Fully convolutional detector

## 4. Detection without Anchors: CornerNet
* Each pixel has a probabilty that determines whether they are the upper-left and bottom-right corners

## 5. Semantic Segmentation
* Painting the object's pixels in the image.
* Limitation of the standard method: If there are two cows next to each other, it will detect the whole region created by the two cows as one cow.

### 5.1 Fully Convolution Network
* You just havea a CNN with no pooling, or downsizing.
* The input is an image, and the ouptu is an image of same size but with scores for each pixel in it.
* Problems
    1. Effective receptive field size is linear in number of conv layers: With a L 3x3 cov layers, receptive field is 1+2L
    2. Convolution on high res images is expensive!

* Solutions:
    * You can build it like an auto encoder, where you downsample all the way to the middle and upsample from the middle to the end :)
    * Downsampling -> Pooling
    * Upsampling -> "Unpooling"
* Ways to upsample 
    1. Computer-vision based:
        * **Bed of nails** : Each value is copied to the corner of its upsampled space
        * **Nearest Neighbour** : Each value fills the upsamples space
        * **Biliear interpolation** : You know it
        * **Bicubic interpolation** : You also know it, instead of 4 control points, you use 16, just like a bezier patch. 
    2. Deep-learning based
        * **Max Unpooling** : You remeber the positions of the pixels from where you pooled them from one layer on this side of the network, and place them in the exaclty same location on their mirrored upsampled version on the other side of the network. This is a much better version of the "bed-of-nails"
        * **Transposed Convolution (Deconvolution)** : (Check lectures for nice diagrams) You overlap the values of the small filter on the larger filter. Overlaptin regions correspond to the sum of their original values. Convlution can be expressed as a matric multiplication (check slides) and, you guessed, the inverse is the transposed versin of the multiplication

### 5.2 Instance Segmentation
* Computer vision researchers separate instances into things: Like people and objects and stuff like land, water, sky, stc. It doesn't make sense to count stuff, only things.
* Object detection only distinguish things, not "stuff"
* Instance segmentation is detecting the things in each of the semantic areas.
* For that you gonna turn a **Faster R-CNN** into a **Mask R-CNN**, that will also output a mask prediction
    *    You run a fully connected semantic segmenation nework from the region proposal network for each of the detected objects. You are effectivelly training object detection and semantic segmentation, all at once!

### 5.3 Panoptic Sementaion
* It's a thing. It segments things and stuff. It effectivelly labels all the pixels in the image

### 5.4 Keypoint estimation (joint tracking)
* You can defind the joints location for each of the people in the image
* You can ALSO modify your **Mask R-CNN** not not only output a mask, but also the keypoint predictions. Is there anything that can't be done by sticking more outputs to a NN? haha
* You now predict a keypoint mask

### 5.5 3D Shape prediction
* You can also 
