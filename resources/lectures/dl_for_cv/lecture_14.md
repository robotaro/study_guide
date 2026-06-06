# Lecture 14 : Visualising and Understanding

## 1. Visualising Filters
* You can look at the features in their original formats and that will show you what features are more proeminent
* You can also use the last layer of the network (the 4096 dimension vector in AlexNet) to organise a nearest neighbour search. So each image you input becomes a 4096 feature vector at the end, and you can retrieve through KNN what other images are closes to that one. Effectivelly, you using the network as an encoder. Of course!

## 2. Dimensionality reduction
* Still using the idea of getting the 4096 (or however large it is) final vector of the network
* Go to town:
    * PCA (linear)
    * T-SNE (non-linear)
    * uMap (non-linear)

## 3. Visualising Activations
* You can also visualise the activation from the layer. So for example you have a 128x13x13 feature map, you can visualise it as 128 13x13 grayscale images
* You can pass all images through the network and select what patches in those images make that filter react! So you'l fine one neuron that reacts to only circles for example

## 3. Saliency vs Occlusion
* **Salience Map** 
    * Get one image, mask part of it and observe how the predicted probabilities change. Then just move that mask around and annotate the probability values for each position of the mask on the image. What you'll get is a map showing which areas are more important to the network on that image. Quite clever :) 
    * This is expensive though, so you can calculate a pretty similar map by computing the gradient of (unormalised) class score with respect to image pixels, taking absolute max value value over RGB channels. CHECK LECTURE FOR MORE
    * This can also be used to remove the pixels that are more important for that classificatoin, I.E cut out the object from the picture

## 4. Intermediate features
* **Guided Backpropagation** : Remove all zeros from both forward and backward passes, effecively combining two masks. The results is those images of filter banks where they are mostly gray, with some salient colorful lines on them. You've seem quite a lot of these representations. To clarify, this shows what pixels on that image active the neurons!
* **Gradient Ascent** : How about creating an image that would maximise the activation of a specific neuron?
    1. Start with a zero image
    2. Forward image to computer current scores
    3. Backprop to get fradient of neuron value with respect to to image pixels
    4. Make a small update to the image

    * You are effectivelly creating "adversarial examples". The images will look like "dreams"

## 5. Adversarial Examples
You can find out what pixels to change in order to fool the network using the same idea
1. Star from arbritary image
2. Pick an arbitrary category
3. Modify the image (via gradient ascent) to maximise the class score
4. Stop when network is fooled
CHECK MORE ON THIS IN THE LECTURE

## 6. Feature Inversion
* Given a CNN feature vecto for a image, find a new image that:
    * Matches the find feature vector
    * "Looks natural" (image prior regularisation)

## 7. Deep Dream
* Basically it amplified recognised features for a particular layer

## 8. Texture Synthesis
* Given a small patch example you want to extend that texture while still matching the initial patch.
* You can use Nearest Neighour approaches (no NN involved, chec 1999 paper)
* **Gram Matrix**
This tells you what feature co-occur with no spatial restrictions. Once you find those features, you just amplify them. Here is how you calculate the Gram Matrix
    * Choose a layer in the network
    * Run your texture through the network
    * The C x H x W tensor of features and calculate the two C dimensional vectors giving you a C x C matrix of elementwise products
    * Average all the HW pairs, I.E you ge the Gram Matrix. This tells you what pairs of features tend to activate together, or not
    * To genetate you minimise the loss betwee the gram matrices from the input matrix and the generator netowkr

## Neural Style transfer
* CHECK THE LECTURE, IT'S AWESOME :)



[[#7. Deep Dream|Explanation]]
