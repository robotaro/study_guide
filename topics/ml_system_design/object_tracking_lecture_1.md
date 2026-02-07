# Lecture 1 : Object Tracking
website: 
[https://www.youtube.com/watch?v=QtAYgtBnhws](https://www.youtube.com/watch?v=QtAYgtBnhws)

## 1. Global Notes:
* Once asked "What are the 3 most important problems in computer vision?", Takeo Kanade replied "Correspondence, correspondence, correspondence" 

## 2. Tracking
* **Appearance**:
    * Single object tracking
    * Re-identification
* **Motion**
    * Trajectiory prediction

## 3. OpenCV Object Trackers 
from: [https://www.pyimagesearch.com/2018/07/30/opencv-object-tracking/]()
* **BOOSTING Tracker** : Based on the same algorithm used to power the machine learning behind Haar cascades (AdaBoost), but like Haar cascades, is over a decade old. This tracker is slow and doesn’t work very well. Interesting only for legacy reasons and comparing other algorithms. (minimum OpenCV 3.0.0)
* **MIL Tracker* : Better accuracy than BOOSTING tracker but does a poor job of reporting failure. (minimum OpenCV 3.0.0)
* **KCF Tracker** : Kernelized Correlation Filters. Faster than BOOSTING and MIL. Similar to MIL and KCF, does not handle full occlusion well. (minimum OpenCV 3.1.0)
* **CSRT Tracker** : Discriminative Correlation Filter (with Channel and Spatial Reliability). Tends to be more accurate than KCF but slightly slower. (minimum OpenCV 3.4.2)
* **MedianFlow Tracker** : Does a nice job reporting failures; however, if there is too large of a jump in motion, such as fast moving objects, or objects that change quickly in their appearance, the model will fail. (minimum OpenCV 3.0.0)
* **TLD Tracker**: I’m not sure if there is a problem with the OpenCV implementation of the TLD tracker or the actual algorithm itself, but the TLD tracker was incredibly prone to false-positives. I do not recommend using this OpenCV object tracker. (minimum OpenCV 3.0.0)
* **MOSSE Tracker** : Very, very fast. Not as accurate as CSRT or KCF but a good choice if you need pure speed. (minimum OpenCV 3.4.1)
* **GOTURN Tracker** : The only deep learning-based object detector included in OpenCV. It requires additional model files to run (will not be covered in this post). My initial experiments showed it was a bit of a pain to use even though it reportedly handles viewing changes well (my initial experiments didn’t confirm this though). I’ll try to cover it in a future post, but in the meantime, take a look at Satya’s writeup. (minimum OpenCV 3.2.0)

## 4. Single Target Tracking (STT) Single Object Tracking (SOT)
1. As a matching/correspondence problem.
    * **GOTURN** (Generic Object Tracking Using Regression Networks)
        * uses current and previous frame do to the tracking
        * It makes the assumption that the object hasn't moved much in the search region.
        * The object moved slightly but it is still inside the frame region
        * The search region runs through the CNN and the object is detected
        * (Pros) Very fast
        * (Cons) This won't work if the object moves outside the search area. So only slow moving objects.
    * **Learning Correspondence from the Cycle-Consistency of Time** (2019)
        * You start with the learning signal and you go back in time search that object in previous frames, you track forward in time and whereever you end up is your loss (L2 maybe). 
2. As an appearance learning problem. You learn/update how the appearance of the object changes over time.
    * **MDNet** : Quick onlin fine tuning of the network (Learning Multi-Domain Convolutional Neural Networks for Visual Tracking - 2015)
        * Tracking happens by repeating:
            1. Drawing target canditated
            2. Find optimal state (like R-CNN)
            3. Collect trining samples
            4. Update the CNN if needed
        * (Pros)
            * No previous location assumption, the object can move anywhere in the image
            * Fine tuning step is relativaly cheap
            * Winner of VOT challenge 2015
        * (Cons)
            * Not as fast as GOTURN
3. As a temporal/prediction problem. You you have an appearance model (CNN) and a model for its movement LSTM
    * ROLO = CNN + LSTM

## 5. MOT Online Tracking
Uses two frames (previous an current)
* Real time applications
* Prone to drifting -> you cannot correct errors in the past
1. Tracking initialisation (using an detector detector)
2. Predict the next position (motion model)
    * Kalman Filter (classic)
    * Recurrent Architectures (nowadays)
    * Constant velocity (works quite well at high framerates without occlusion)
3. Matching predictions with detection
    * A similarity measure

## 6. MOT Offline tracking
Uses a series of frames
* Can recover from occlusions because it can see in the future
* Suitable for video analysis
* Improving appearance models : Re-identification
* Matching still happens separately from lerning

## 7. Trackers:
* Two-step regressors:
    * Received a region proposal
    * Convolves on the region
    * Provides a probability distribution over classes and a bouding box
    * Adjust bounding box with proposed

### 7.1 Trackor (ONLINE):
* You can use bounding box regression form our detector to do tracking
* PROS
    * You can you extremely good detectors (like Faster R-CNN) to create well-positioned bouding boxes
    * You can train your model on still images (easier to annotate)
* CONS
    * It's not really a trackinc method as it doesn't have a notion of "identity". On a crowded scene it will snap to any pedestrian
    * Tracker cannot recover form occluded scenarios
    * The regression only shifts the box by a small quantity. If the targe moves too quickly, or the camera moves, you will not be able to regress bouding box position from position t to t-1. Or if the camera is low FPS
* NOTES:
    * There is a lot of research right now on **Re-Identification**
    * Motion model can tell us how the camera is moving, and how the pedestrian is moving

### 7.2 

## 8. Re-Identification
* Researchs re-formulate the problem of multiple tracking as a "retrieval problem"
* You detect one person in one image, and you now have to matching this person to a set of matches from another image. For this, you need to find the "closest" image match. But how do you calculate the distance between images? Distance of RBG histogram? No, go for deep learning
* You can use **Similarity Learning**, where you train a netowork to provide a distance measure between two images. This is called **Deep Metric Learning**. 
* If you treat this as a classificaotin problem, you provide images of each object and train the network like any other classification problem, but this is not scalable. You'd have to re-train the network every time a new object is added.
* Instead, you train the network to learn the similarity between pictures of the same class of objects. It will learn to return high similarity for two pictures of the same object, and low similarity for a different one. You then use the similarity score to decide on whether that is a real match.
### 8.1 Training a **siamese network** using the **Contrastive Loss**:
* In a siamese network, where two images are fed, and each goes through a CNN and FC layer. But both networks have the same weights, and are trained at the same time.
* If A and B are the same object, the loss is small and you use L2 distance.
\[\mathbb{L}(A,B)=\left\| f(A)-f(B)\right\|\]
where f(A) and f(B) are the feaure representation of images A and B respectivelly 
*If A and B are different objects, the loss is large and you use a Hinge loss
\[\mathbb{L}(A,B)=max(0,m^{2}-\left\| f(A)-f(B)\right\|^{2})\]
"m" is a hyperparameter called the margin. If two elements are already far away, don't spend energy pushing them even further away. They increases until they meet the margin and then the loss is zero.
* Putting both losses together you get the **Contrastive Loss**
\[\mathbb{L}(A,B)=y^{*}\left\| f(A)-f(B)\right\|+(1-y^{*})max(0,m^{2}-\left\| f(A)-f(B)\right\|^{2})\]
where y* is the positive pair, and (1-y*) is the negative pair

### 8.2 Training a **siamese network** using the **Triplet Loss**:
* THis looks at 3 images at the time, the anchor (A), positive (P) and negative (N). Which is effective a ranking problem, where you want to rank images of the same object with lower loss than of different objects. 
\[\left\|f(A)-f(P)\right\|^{2}<\left\|f(A)-f(N)\right\|^{2}\]
* So we can re-write it and add a margin hyperparameter, just like before.
\[\left\|f(A)-f(P)\right\|^{2}-\left\|f(A)-f(N)\right\|^{2} + m < 0\]
* And just like before we can now use the Hinge loss to make sure we move that error up to the margin value. You are effectively separating the dist(A,P) and dist(A,N) by a margin m.
\[\mathbb{L}(A,P,B)=max(\left\|f(A)-f(P)\right\|^{2}-\left\|f(A)-f(N)\right\|^{2} + m, 0)\]
    * If dist(A,P) > dist(A,N), the loss is positive
    * If dist(A,P) < dist(A,N), then you are looking at the marging
* Train it with hard cases where dist(A,P) ~ dist(A,N) to refind the distance learning
* In other words, we're pulling the negative away from the anchor and the positive loser to the anchor

### 8.3 Limitations with Constrastive and Triplet trainings:
* Number of pairwise relation in a mini-batch is O(n^2), but contrastive loss is O(n/2), and the triplet loss is O(2n/3)
* Too much information is thrown away
* Many tricks are required to train these networks:
    * Hard-negative mining
    * Intelligent sampling
    * Multi-task Learning

### 8.4 Training using the **Group Loss** (The Group Loss for Deep Metric Learning - 2019):
* We want to take the similarty of all the samples againsts all the other samples in the mini-batch, no more choose pairwise distances
* Training uses the same principle as sharing CNN weights, then into a FC layer, and now through a softmax that feeds into a "Classes Prior" part of the loss, whereweres the features from the FC feed into a "Similarity" part of the loss.
* Procedure
    1. **Initialization** : You assign a label to each image to improve the embeddings in order to avoid the hand-picking
    2. **Refinement** : You compute a pair-wise similarity matrix W between all samples in the mini-batch, for N samples it is a NxN matrix. You then refine the labels of the images using the similarity marked in W. If W tells me two images are similar, they should have the same label. You use this to correct the labels assigned by the previous softmax functions using the W similarity matrix
    3. **Loss computation** : The loss is cross entropy, just like for a normal classification problem. You update the labels estimation iteratively for a few steps and you then update the label if necessary. 
        * This uses what is called **replicator dynamics**, a iterative process for propagating information:
        \[x_{i\lambda}(t+1)=\frac{x_{i\lambda}(t)\pi_{x_{i}\lambda}(t)}{\sum^{m}_{\mu=1}x_{u\mu}(t)\pi_{i\mu}(t)}\]
        Where lambda is the class, the denomiate is the information to be propagated and the denominator is for normalising it in order to stay in **standard simplex**. 
        * w is your similarity matrix for this equation that plugs into the previous one. You are performing doing a weighted sum of prior probabilities:
        \[\pi_{i\lambda}=\sum^{n}_{j=1}w_{ij}x_{j}(t)\]
        This function measures the support the current mini-batch that image i depicts the class lambda
        * The Group Loss has no parameters to learn, but it propagates the gradients over the entire network.

## 7.2 ROLO
* Recursive YOLO


