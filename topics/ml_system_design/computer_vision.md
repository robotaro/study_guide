# Computer Vision

## 0. Camera calibration

* Calculating the projection matrix (3x4)
    * Get points on 2D space 
    * Scaling the projection matrix is scaling the camera
    * You can solve the list of equations using Constrained Least-Squares. You solve it by minimising the loss function that has a main function and a constrained function.
* Calculating the Intrinsic(3x4) and Extrinsic(4x4) matrices:
    * You get these two matrices from the projection matrix. The Intrinsic matrix contains the internal parameters and the extrinsic matrix the external ones.
    Projection = Intrinsic x Extrinsic

## 1. Image Processing

### 1.0 Preprocessing

* **Median Filter** - removes salt and pepper noise. It sorts the elements per ROI and write the median value
* **"Bias" Gaussian smoothing**
    * It smooths only the similar areas within leaving the edges intact
    * You mask the gaussian kernel with only pixels of similar value to the one at centre of the kernel 
* **Bilateral Filter**
    * You add a new term to the kernel, the brightness Gaussian or Range filter, that weights each pixel according to its neighbourhood. 
    * This kerner changes on a pixel per pixel basis
    * Very common on photoshoped images

### 1.1 Morpholical operations
* Thinning
* Thinkening
* Erosion
* Dilation

### 1.1 Filters
* **Roberts** for edge detection, 2x2 with [[0, 1], [-1, 0]], very bad
* **Prewitt** for edge detection, 3x3 but with only 1, 0 and -1 values on the same location as sobel
* **Sobel** for edge detection, a vertical and a horizontal one
* **Laplacian** Used for edge detection     
    * computes the second derivative. The edge is the zero-crossing at the middle
    * Does not provide orientation
* **Gaussian** for blurring. Used as a preprocessing step for removing noise. It can be split into two operations (x and y) so that the final 2D result is the product of both 1D Gaussians
* **Difference of Gaussians (DoG)** for edge detection, it aproximates (a scaled version of) the Laplacian of Gaussians (first derivative after gaussian filtering)
    1. You blur you image with gaussian kernels of two different sizes.
    2. You then subtract the blurred images and are left with the approximate Laplacian of gaussians
    * This is preferable to applying a gaussial filter and then taking a laplacian as that takes three operations. You can just do the subtractions and that is two operations.

* **Canny Edge detection** 
    * Most well used edge detector in computer vision
    * Combines the best of the gradient operator (sobel) and laplacian operator
    * Steps:
        1. Apply Gaussian on the image
        2. Computer Gradient using the Sobel operator at each pixel
        3. Calculate the magnitude of the gradient at each pixel (hypotenuse)
        4. Calculate the gradient orientation (angle) at each pixel
        5. Apply 1D laplacian along the direction of the edge for that pixel. Interesting, so that will force the laplacian to only react to edge changes on that direction. Clever!

### 1.2 Boundary detection

#### 1.2.1 Fitting lines and curves to edges
* Least squares fitting for both linear and polinomial curves
#### 1.2.2 Active contours
* Approximate the contour of  the object iteratively
* Very common in medical imaging and for tracking objects ob video. The contour from one frame can be used in the next frame
* The contour is defined by control points. These control points are placed based on the gradients of the edged, and forced closes to the object by blurring the gradients and using that as a force-field to attract the active contours
* It is sensitive to noise and can cause twists, so you want to add elasticity and smoothness
    * From one control point to another it can't move too fast (first derivative)
    * Minimise the rate of change (second derivative)
    * These are computed using finite differences
* Active contours still need a good initialisation

#### 1.2.3 Hough Transform
* You know it
* Accumulator (voting table) returns lines with highes overlapping parameter intersection.
* For lines, A point in the accumuator defirnes a line's ro and theta parameters for a line
* For circles of known radius, the highest voting point is the centre of the circle itself
* If you have the edge direction of the point, you can then fit a circle to a part of a curve. Your voting space is the point defined by the edge direction from the initial point to the radius of the circle  
* For unknown radius, the amount of computation grows exponentially
#### 1.2.4 Generalised Hough Transform
* You can use x and y voting table for different silhuette shapes (2D votint space still)
* For rotation and scale you are now voting in 4D space - very very memory hungry and expensive

## 1.3 Polygon Segmentation
- Voronoi diagram

## 2. Object detection

### 2.1 Region Proposal
* **Selective Search** (segmentation as selective search for object recognition - 2011):
    * This was used as the first stage of the first R-CNN object detector
* **Edge Boxes** (edge boxes location object proposal from edges - 2014)

### 2.4 Important Features and Algorithms

#### 2.4.1 Integral Image
    * Any pixel value on the integral image is the result of the sum of all the pixels from that point to the left upper corner of the image. Check [https://www.youtube.com/watch?v=5ceT8O3k6os]
    * You compute the value of each pixel of theintegral image by addint the values of the already-computed pixels to the left, above and left-above pixel.
    * Once you compute the integral image, you can calculate the sum of pixels of any area of the image, regardless of the size of the image, using two subtractions and one addition usong the 3 other pixels that define the left and upper bounds of the area you are interested. 

#### 2.4.2 Haar Features
    * Mainly used for face detection. These are hand-crafted filters(kernels) were designed to fit nose and eyes.
    * Once their highes values overlap, that is where your face is. 
    * The filters use the sum of pixels from sub-areas in the search region to calculate a feature value for that area. You often see black and white patterns for each of the Haar filters, and each will responde to a feature that is brighter and darker on tohse areas when aligned. Effectivelly a kernel that responds to the a certain combination.
    * To calculat ethe feature, you calculate the average the pixels on the white and dark areas and perform a "mean_dark - mean_white". Ideal dark and white are respective 1 and zero
    * Once you get the Haar feature vector (one feature per filter) you can use that to classifier the ROI

#### 2.4.3 Viola and Jones : Boosted Decision Trees for face detection
1. Select your Haar-features
2. Integral image for fast feature evaluation. Detect which part of the image have highest cross-correlation with my feature (template).
3. Adaboost to find weak learner (stomp). 
    * You can't evaluate all features at test time for all image aplications
    * Lear the best set of wek learners
    * Final classifier is the combination of the weak classifiers  
* NOTES: They work really well for faces, but ONLY when facing forward

#### 2.4.4 Histogram-of-Oriented Gradients
Calculating HoG
1. Compute edge direction / strength at each pixel
2. Divide image into 8x8 regions
3. Within each region, compute histogram of edge directions weighted by edge strength
Training happens between images (areas) with and without the person
#### 2.4.5 Deformable Part Model
Also for detecting people and also based on HoG features, but based on body part detection. You can use ir to detect people in different poses

#### 2.4.6 Scale Invariant Feature Transform (SIFT)
* Originally designed in the 90's.
* Allows for matching features that are invariant under scale and rotation. You can use this to match text from different photos under different points of view
* You choose a **keypoint** in the image and create local vector, called a **descriptor** that describes that local area
* The normalised laplacian of gaussian is sensitive to different types of blobs/edges based on the std(sigma) of the gaussian function. A small sigma will responde to lines, and larger sigma to stripes. The peak result from the laplacian of the gaussian will give you a lower negative value for the blob it is designed to detect. Interesting!
* **Calculating the Keypoint** - For different resolutions of the image you Calculate the Difference of Gaussians with different kernel sizes and stack them up. Then you look for distinct points that are the same across the stack. This will give you key points that are unique and common across scales, hence scale-invariant
* **Calculating the Descriptors** - The descriptors are the HoGs of the DoG images. You consider the histogram of orientations. The hypothenuse is ignored because it will vary according to the exposure, lighting condition etc.
    
* Algorithms that use SIFT features:
    * Bundle Adjustment
    * Stereo matching
    * SLAM
    * Bag-of-Words
    * Loop closing (not an algorithm, but in general)

#### 2.4.8 Non-Max Supression:
When you have multiple overlaping boxes to select from, some of them represent the sam object so you need to select which one(s) is(are) correct
* With a narrow threshold : You may be able to find close by ground-truths, but you may have false positives. High recall, low precision
* With a wide threshold : You may remove some ground-truth nearby. Low recall

#### 2.4.7 Harris Corner Detection
* Designed for detecting corkers, like on chessboards sets
* How to computeL
    1. Take the derivative of the X and Y  axes of the ROI. This ROI is centered on a pixel and could be sliding window, sectors, etc.
    2. Fit an ellipse on the point cloud created by. If the points with coordinates (dx, dy). So it's one point per pixel.
    3. The ellipse will produce a lambda_1 and lambda_2 values, that you can then use to determine, based on their position on a plot, whether that ROI contains an edge. The ROI is determined to be an edge if the R value is greater than the threshold T.
    \[R=\lambda_{1}\lambda_{2}-k(\lambda_{1}+\lambda_{2})^{2}\]
    Check link for image [https://www.youtube.com/watch?v=Z_HwkG90Yvw]()
    4. Use Non-Maximal Suppresion

### 2.5 Important image segmentation techinques and algorithms
#### SLIC (Simple Linear Iterative Clustering)
* Used to create "superpixels"
* Definitions
    * N = NUmebr of images pixels
    * K = amount of superpixels
    * N/K = agerage area of superpixels
    * S = sqrt(N/K) distance between centers
* One superpixel is defined as:
\[C_{k}=[R_{k},G_{k},B_{k},x_{k},y_{k}]\]
* The distance function D_xyis definde by
\[d_{RGB}=\sqrt{(R_{k}-R_{i})^{2}+(R_{F}-G_{i})^{2}+(B_{k}-B_{i})^{2}}\]
\[d_{xy}=\sqrt{(x_{k}-x_{i})^{2}+(y_{F}-y_{i})^{2}}\]
\[D_{xy}=d_{RGB}+\frac{m}{S}d_{xy}\]
* Algorithm:
*   Initialise clusters centers C_k..n
    * Repeat until stability
        * For each C_k
            * Find similar pixels in 2S x 2 neighbourhood using the distance function
        * Compute new centers

#### Conditional Random Fields (CRF)
* This is a very useful algorithm for taking interactive annotaions from the user for labelling foreground/background. The user draw a line on the foreground, and on on the background and it algorithm "expands" the selection.

## 3. Single Object Tracking
* When tracking an object, you want to keep track of the hypothesis, but this is impossible as the number of hypothesis grows exponentially with time.
* Estimating the posterior density distribution of hypothesis is also impossible because even if you can aproximate the true density of the distribution as a gaussian, it would require too many components.
* What we do is  
* The next 3 algorithms are examples of **Assumed Density Filters**. NN and PDA are Gaussian Densities and GSF is Gaussian mixture of densities.

### 3.1 Nearest Neighbour (NN) Filter
* For **Prunning**

### 3.2 Probabilistic Data Association (PDA) Filter
* For **Merging**

### 3.3 Gaussian Sum (GSF) Filter
* For **Prunning and Merging**

### Gating
* This is a technique to disregard detections that may appear in impossible/very unlikely locations

## 6. Random Finite Sets
* Here, the number of objects and their states is unknown. They can appear and disappear, they are not ordered and we cara bout ths states
* We represent the states of the objects (which can very) as **set**, not a matrix. Why?
    * Because it's invariant to order
    * Elements can be easly added/removed
    * The set of state vectors is our quantity of interest
    * Easy 1-to-1 relation between physical reality and the set



## 5. Poisson Point Process

## 5. Poison Birth Model
* check [https://www.youtube.com/watch?v=q0J90vq6cFE]()
* Models the probability of a point coming to existence in the ROI
* This uses the Poison Point Process Birth Model, which uses a Gaussian mixture model
    * The more mixture modes you have, the more specific the poison point process model, but the higher the computational cost.
* Poisson intensity has a mixture representation, a weighted sum of densities. So you add all the weighted individual gaussian functions to create the Poisson intensity (Gaussian Mixture Birth Intensity)
* For example, if the object moves according a gaussian motion, it makes sense to have a estimation as a mixture of gaussians
* With the birth estimate, you can cover the areas where you expect object to appear with gaussian distributions, and the the mixture of all Gaussians is the normalised to add to the expected number of births. Not all components have to have the same weight
* 

