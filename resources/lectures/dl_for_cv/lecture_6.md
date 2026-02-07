#backpropagation
# Lecture 6: Backpropagation

## Calculation Gradients
* (Bad) You can try to derive the loss function on paper
* (Good) Computational Graph, the standard today. Separate the weight calculation, loss and regularisation into nodes, and use that to derive the loss value at the end