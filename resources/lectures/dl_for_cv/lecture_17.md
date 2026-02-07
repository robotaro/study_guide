# Lecture17 : 3D Vision
[https://www.youtube.com/watch?v=S1_nCdLUQQ8&list=PL5-TkQAfAZFbzxjBHtzdVCWE0Zbhomg7r&index=17](Youtube Video)

## 1. Predicting Depth maps
* Use our fully convolution network, the one that downsamples and upsamples and trained to to predict the depth image instead
* The loos product is the L2 norm
* However, you can't tell the different between  a small close object and a large far away object. This is called **scale/depth ambiguity**
* To sovle the scale ambituity, you can use a "scale invariant loss" (check slides)

## 2. Predicting Surface Normals
* The loss functoin is a dot production between the ground truth and predicted vectors

## 3. Predicting Voxels
* Conceptually simple
* Limitations:
    * Scalling to high resolution is nontrivial
    * Takes toooo much memory for high resolution grids
* 3D convolution... nooo
* Solution is to use a **voxel tube** network representation, so 3D from 2D by prediction the depth of the voxel

## 4. 3D Shape representations
* **Voxels**
* **Implicit surface** : Mathematical function that represents the surface - A Signed Distance Function (SDF), which gives the euclidian distance between a point in 3D space and the surface. Positive if outside and negative if inside. You can now train a NN to aproximate the Implicit surface. Nice! 
* **Pointcloud** : You know it. Check **PointNet** network
* **Mesh** : You can use iterative mesh deformation using a NN as well. You move the triangles on the mesh, see how much it matches the target image, and try again, and again, and again for the training. You will also need "graph convolution" to replace the normal 2D convolutions