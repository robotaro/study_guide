# Lecture 12 : Recurrent Neural Networks

## Sequential processing
* Up to now, we've been working with feedforwad networks for image classification.
* Tasks like image captioning may require a one-to-many architecture
* Video classificaiton may require a many-to-one architecture
* Machine translation uses a many-to-many, like for translation
* Per-frame video classification, many-to-many, sequence of images to sequence of labels
* You can use Recurrent Neural networks for sequential processing, like analysing a digit by following the line. Interesting :) 
    * It takes multiple glimpses along the image, like following lines and other features. The direction is determined by the network itself
    * This can also be used to generate images
* RNNs have an "internal state" that is updated at every step
\[h_{t}=f_{W}(h_{t-1},x_{t})\]
where h_t-1 is the old stante, x_t is the inpute vector and h_t is the new state

## Truncated Back Propagation Through time
* Calculate loss per chunks of images

## Image captioning
* You feed in 3 inputs at each time step of the network
1. Current element of the sequence that we're processing
2. Previous hidden state
3. Feature extracted from the top (end) of the CNN - pretrained on imagenet

# Long Short Term Memory (LSTM)
* We now keep two hidden states, the cell state and the hidden state
 GO BACK TO THIS LECTURE FOR MORE DETAILS!!!