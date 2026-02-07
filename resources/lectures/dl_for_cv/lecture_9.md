# Lecture 9 : Hardware and Software
* Tensor cores can perform the follow 4x4 matrix operations, AB+C in ONE CLOCK CYCLE! O_O
*  

## Programming GPUs
* Cuda (nvidia only)
    * NVIDIA provides APis, cuBLAS, cuFFT, cuDNN
* OpenCL
    * Similar to cuda but runs on anything
    * Usually slower on NVIDIA hardware

## Frameworks
* CAffe 2 (Facebook
* PyTorch (Facebook)
* TensorFlow (Google)
* etc

## PyTorch Concepts
* **Tensor** : Liek a numpy array, but can run on the GPU. This is all you need to build networks that run on the GPU!
* **AutoGrad** : Package for building computation graphs out of tensors, and automatically computing gradients
* **Module** : A neural netowrk laayer; may store state or lernable weights
* Pytorch cannot use TPUs, and is not easy to use on mobile applications

## Static vs Dynamics Graphs: Optimisation
* You can combine conv + relu if your graph is static.
* **Static** 
    * can be serialised and run without the code
    * Hard to debug
* **Dynamic** 
    * Needs to keep the code around tobe rebuilt
    * Easier to debug

## NOTEs:
* Check his slide for a simple for loop on ow to train a network using pytorch
* Also check code for Tensorflow
* Don't forget to use TensorBoard :)