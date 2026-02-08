
#diffusion #autoregression

- Have a look at this video: https://www.youtube.com/watch?v=zc5NTeJbk-k
- Predictions are like inverse regression models:
	- In a regression you have an input and you generate a value from it following a learned function
	- The generator would generate the input for the output value you provide, but if there are multiple input values that would generate that output, they will be averaged
- The image *auto-regressor* predicts one pixel color at a time, based on all the previous pixel colors (in sequence) previously predicted (REally? Wouldn't that take a long time?)
	- ChatGPT is an auto regressor!
	- Auto-regressions are no long used to generate images!!!
- Denoising Diffusion are identical to auto-regressions in that they try to reconstruct information, but unlike auto-regressions where you remove information at each stage to train the regressor model, you add noise at each stage to train the diffusion model. Each noise stage is a blend 
- The noise is the label! Modern diffusion architectures try to predict the noise from an input image:
![[Pasted image 20240513010139.png]]