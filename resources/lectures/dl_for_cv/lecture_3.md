# Lecture 3
- If you reshape the weights of linear classifiers into the shape of the original image, you are effectivelly showing a "template matching"
- A loss function (objetive function, cost function, etc) is a way to quantify what your model is doing well
- Regularisation: Prefer simpler models - This should help with overfitting, Occam's Razor
- A Hard Max function is the one-hot encoding, it is non-differentiable. 
- A Soft-Max function is differentiable and we can use that on our gradient= descent-based optimisation loop. You use this when you want to compute the max, but you also want it to be differentiable
- You can used softmax function to estimate whether something went wrong with your optimisation by looking at the cross entropy value when all weights are random. You should see a cross entropy of -log(1/num_class), which for 10 is log(10) ~ 2.3.  Minus goes inside as the expoent of the contents, so contents^-1
- There is no actual way you can achieve zero-loss using cross-entropy (double check this statement)