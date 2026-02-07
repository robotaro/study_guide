# Lecture 15 : Object Detection

## 1. Task Defintion
* Object detection task will return a catagory label and a bouding box with four numbers x, y, width, height
* classificatoin works on 224x224 (alexnet?) images, and images are much larger
* **Multitask Loss** : For one object and one bounding box, the same network,AlexNex for example, should output both label and bounding box location, and each would have its own loss function. So we can use a weighted sum of both losses into one for the training 

## 2. Sliding Window
* A pre-defined size window that moves over the picture and it classifiers each region. 
* But how many window sizes and positions? A LOT!

## 3. Region Proposals
* Find a small set ofboxes that are likely to cover all objects
* Often based in heuristics, like look for "blob-like" image regions, like for color and edges
* Check algorithm "Selective Search 2000" - nobody uses it anymore. It's history

## 4. Region-Based CNN (R-CNN)
One of the most influential papers in deep learning (2014)
1. Create candidate regions (ROI) using one of the proposal methods
2. For each region, warp it to fit a fixed size, like 244x244
3. Each region will run through the CNN and generate: 
    * A class probability
    * A "transform" to correct the ROI (t_x, t_y, t_h, t_w) 
    We're not reinventing the region, we're proposing a tweaked version of the bouding box that should fit better the object. Also the input ROI should be expresed in width and height only **in order to be location invariant**
4. Compare proposed and ground-truth boxes

* Maybe output the top 10 regions with the lowest background score (different between proposed and ground truth). Or set a threshold for the class probability, etc. It will depends on your application.
* Training is done on all regions proposals, the thresholding for the final detection is done on the test images
* Comparing bounding boxes: **Intersection over Union (IoU)**
    \[\frac{Area of Intersection}{Area of Union}\]
    * Also know as jacard similarity
    * IoU > .5 is okay, decent
    * IoU > 0.7 is pretty good
    * IoU > 0.9 is amost perfect
* Solving the overlappping boxes problem: **Non-Max Suppression (NMS)**
    Object detections may proposed many overlaping boxes, so you need so post procesing get rid of them
    1. Select highest-scoring box
    2. Compute of IoU score and every other box
    2. Elimiate low-scoring boxes with IoU > threshold (like 0.7). We are targeting removing duplicated boxes near the best box.
    3. if any boxes remain GOTO 1
    DRAWBACK: This could remove some good boxes when there is true overlap. Like in crowds. There is no current good solution for this :(

## 5. Evalutation Obejct Detectors: Mean Averge Precision (mAP)
1. Run object detector in all test images (with NMS)
2. For each category, compute Average Precision (AP) = Area under "Precision vs Recall" curve
    1. For each detection  (highest to lowest score)
        1. Match the detections with their respective ground trugh boxes with IoU > .5, and elimiate that ground truth
        2. Otherwise mark it as negative (you may have more detections than ground truth boxes, so you use the best ones you have)
        3. Plot that as one point on the PR curve
    2. Average Precision (AP) = Area under the PR curve
3. Mean Average Precision (mAP) = avarage of AP for each category
4. For "COCO mAP": COmputer the mAP@thresh for each IoU threshold (0.5, 0.55, 0.6...0.95) and take the average. And you're done! Phew!
    
## 6. Fast R-CNN
* R-CNN is rather expensive, as it has to run one forward pass of a CNN for each proposed region!
* The Fast R-CNN swapps the image warping from the proposed regions with the conv step. Here it how it goes:
    1. Run a pre-trained network withoug the final fully connected layers, so just the conv layers - also called the **backbone network** This will give us our "convolutional feature map" for the entire high-res image. You can use Alexnet, ResNet, etc.
    2. Run your region-proposal method on the original image, BUT project the ROIs on the feature space! So you don't have to run a whole CNN for that ROI, but rather take the features already calculated for that ROI :) very nice!
    3. Crop + Resize the projected ROIs on the feature map itself
    4. Run a very light weight CNN on those feature ROIs

### 6.1 Cropping Features: ROI Pool
1. Run a CNN and produce a smallre version of the original image, with C x H x W, so now each pixel in this feature space is an area on the orignal image
2. The proposed region on the original image is snapped onto the grid in this feature space.
3. Divide the snapped region, like 2x2, subregions of roughly equal size
4. Perform Max-pooling in each of those sub-regions (so you get one the highest value per subregion). In practice you use something like 7x7
* Region features

## 7. FastER R-CNN : Learnable Region Proposals
* A new "Region Proposal Network" is insertaed beterrn the feature map and the RoI pooling.
### 7.1 Region Proposal Network (RPN)
* At each point in the feature map, you can image an anchor box and for each anchor box in every pixel on that map you train the CNN to classify it as "object" or "no object.
* You wil also output a proposes transform to try to improve the size of the anchor box to a size we going to use
* In practice you will use anchor boxes of different sizes
THERE IS MORE ON THE LECTURE!

So, there are 4 losses:
1. **RPN Classification** : Anchor Box is aobject / not object
2. **RPN Regression** : Predict transform from anchor box to proposal box
3. **Object Classification** : Classify proposals as background / object class
4. **Object Regression** : Predict transform from proposal box to object box

* Faster R-CNN is a two stage object detector:
    * Stage 1: Run once per image (backbone network and RPN)
    * Stage 2: Run once per region (crop features, predict object class, prediction of bounding box offset)

### 7.2 Single Stage Object Detection 
* This gets read of the second stage!
* Instead of classifying the regions as object/not object, you can just try to classify them as a class or background.
* This is a category-specific regression
* One-stage methods methods today are almost as good as two stage-methods. Maybe the same now?
* Two-stage methods are slower than single-stage methods