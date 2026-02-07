
from: https://www.markovml.com/blog/lime-vs-shap
and 
https://medium.com/@bhattacharyya.shilpi.sbu/explaining-black-box-models-ensemble-and-deep-learning-using-lime-and-shap-53c59d9f09b3

![[Pasted image 20240810143403.png]]

# LIME vs SHAP

The black box of machine learning models is essential for building trust and understanding their predictions. In this comprehensive guide, we delve into the intricacies of two prominent interpretability tools - LIME and SHAP. In the field of explainable AI, LIME (Local Interpretable Model-agnostic Explanations) and SHAP (SHapley Addictive exPlanations) offer unique approaches to complex model outputs. 

Let’s learn about their methodologies, applications, and comparative analysis, empowering data scientists, researchers, and enthusiasts to make informed choices when seeking transparency and interpretability in machine learning.  

## Understanding
### **LIME**

LIME, or Local Interpretable Model-agnostic Explanations, is a technique that generates local approximations to model predictions. 

Example: In the process of predicting sentiments with a neural network, LIME highlights important words in a specific prediction. 

### **SHAP**

SHAP or SHapley Addictive exPlanations is a technique that is used to assign a value to each feature, indicating its contribution to a model’s output. 

Example: Credit scoring is a good example as it utilizes SHAP to reveal the impact of variables like income and credit history on the final credit score. 

## Importance of Interpretability

- Trust and Reliability: Users gain confidence when they understand model decisions. 
- Compliance and Regulations: Essential for meeting regulatory requirements. 
- [Debugging and Improvement](https://www.markovml.com/blog/ml-model-debugging): Enables identification of model weaknesses for refinement. 

Understanding [model interpretability](https://www.markovml.com/blog/model-interpreting) is integral for fostering trust, meeting regulatory standards, and refining models for optimal performance. By employing tools like LIME and SHAP, practitioners can navigate the complex terrain of ML with transparency and clarity.

That’s where the [MarkovML](https://www.markovml.com/responsible-ai) no-code platform helps organizations experience unparalleled transparency in comprehending and explaining the outcomes of their AI models.