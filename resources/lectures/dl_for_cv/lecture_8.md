# 8. CNN Architectures
##AlexNet (2012)
##ZFnet
* A bigger alexnet
* Bigger seems to work better
## VGG Architecture 
* New ideas! two 3x3 back to back have an effective coverage of a one 5x5 with fewer operations! 
* Design rules:
    * All conv are 3x3 strid 1 and pad 1
    * All max pool are 2x2 stride 2
    * After pool, double the number of channels

## GoogleNet
* Heavely focused on improving performance on the VGG architecture to be used in new networks
* Uses a "stem" network that agressivelly reduces the size of the input image
* Introduces an "inception module" that gets rid of choosing the size of kernels because it does all operations in parallel within a module. Throwing everything at the wall to see what sticks, are we?
* Also uses 1x1 convolutions to reduce the number of channels before a convolution stage
* Global average pool is also introduced, to collapse spatial dimensions

## Residual Networks
* Up to 152 layers!
* Much deeper networks seem to overfit on the test data, but in fact thy are under-fitting on the training data!
* A deeper model can emulate a shallower mode: Copy layers form shallower mode, set extra layers to identity, this deeper models should do at leas tas well as shallow models.
* The hypothesis was that this was an **optimisation problem**. Deeper models are harder do optimise, and in particualr, they don't learn identity functions from emulated shallow models
* The solution was to change the network so that learning identity functions with extra layers is easy! They added a "shortcut" where a parameter skipped two layers.
* ResNet copies the best ideas from VGG and GoogleNet
* It introduces the "bBasic" residual block (two 3x3 conv back to back), a "bottleck" residual block with a 1x1 conv, then a3x3 conv and a one 1x1 conv at the end

## Ensemble Models
* In 2016, the winners used an ensemble of the most popular methods

## ResNext
* Introduced adding residual blocks in parallel! G parallel pathways that have the same computing cost as one residual block (really? double check that)

## New trends: MobileNets
* Tiny network with minimal computation
* Deepwise convolution and pointwise convolution blocks
* ShuffeNet, ShuffleNetV2 and MobileNetV2

# Neural Architecture Search

* They use a controller network that will sample a network architecture with probability p, train it, compute the gradient p and scale it by R to update the controller, and do it again - policy gradient approach.
* Subsequent papers focused on more efficient methods for searching a better neural architecture