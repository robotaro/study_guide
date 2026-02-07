This is the code written based on Andrej Karpathy lecture:

Let's build GPT: from scratch, in code, spelled out.
https://www.youtube.com/watch?v=kCc8FmEb1nY

This GPT is character-based, so it only predicts the next character, instead of the next word (token). 

It's much easier to train

## GLOSSARY
- **Codebook** : 
	- The "map" of your vocabulary. It can be per character, or per pair os characters, subwords, words, etc.
- **Blocksize**: 
	- The size of tokens that will go into the transformer. The transformer will never received from inputs then blocksize.
- **Bash Dimension**: 
	- Blocksize is thet time dimension along a text, but bash dimenions is the
- **Bigram language model**: 
	- In the Bigram Language Model, we find bigrams, which are two words coming together in the corpus(the entire collection of words/sentences).