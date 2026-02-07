
#modeseeking

- From:  https://www.youtube.com/watch?v=Bpgloy1dDn0
	- Pre-training of LLM is distribution matching
	- Then you do "RL chef" which is "mode seeking", which essentially chops down the reachable space given a prompt by snipping-off all those trajectories
- **Dynamic Context Injection**
	- LLM models are not AGI! You need to dynamically update what prompts go in with relevant information in order to get your prompt to be answered as successfully as possible! For example, in your custom API, you may want to handle extra bits of information about the world's history and its current state, and add these bits of information whenever relevant to the conversation.
- Prompt Template:
	-  How you use keywords such as "system", "user" "assistant" and "instruction" when encoding information into the model. Certain models are trained as "chat", and others as "instruct" so they have slightly different templates
- **Orthogonal Activation Steering (OAS)**
	- 


## From ChatGPT4o:

- ### Pre-training of LLM (Distribution Matching)

In the context of large language models (LLMs), pre-training involves training the model on a vast corpus of text data. The objective is for the model to learn the statistical properties and distribution of the language it is trained on. This phase can be described as "distribution matching" because the model is learning to predict the likelihood of word sequences in a way that matches the distribution of the training data. Essentially, the model is being trained to understand and replicate the patterns, structures, and probabilities of the language it encounters.

### RL Chef (Reinforcement Learning, Mode Seeking)

"RL chef" refers to the use of Reinforcement Learning (RL) techniques to further fine-tune or adapt the model. In this context, "mode seeking" refers to the process of making the model more focused and precise in its responses. Here’s how it works:

1. **Reinforcement Learning (RL)**: After pre-training, RL can be used to fine-tune the model’s behavior based on specific goals or rewards. This involves defining a reward function that encourages desirable outputs from the model.
    
2. **Mode Seeking**: This process aims to make the model's responses more targeted. Instead of just generating text that matches the distribution of the training data (which can be broad and diverse), mode seeking fine-tunes the model to produce more specific, high-quality responses that align with desired outcomes or objectives.
    

### Snipping Off Trajectories

The statement "snipping-off all those trajectories" refers to narrowing down the possible responses the model can generate given a prompt. In the context of mode seeking through RL, the model is trained to favor certain types of responses (those that are deemed higher quality or more relevant) while avoiding others. This effectively reduces the range of potential outputs ("reachable space") by eliminating less desirable paths the model might take in generating a response.

### Putting It All Together

1. **Pre-training (Distribution Matching)**: The model learns to understand and generate text that matches the statistical properties of the training data.
2. **Reinforcement Learning (Mode Seeking)**: The model is further trained to refine its outputs, focusing on producing high-quality, specific responses.
3. **Snipping Off Trajectories**: The RL process narrows the model's potential responses, ensuring it generates outputs that are more aligned with desired outcomes, reducing the variety of responses to those that are most useful or relevant.