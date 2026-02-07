#llmleaderboard
## Introduction

LLM Merging is combining weights from different pre-trained model into a fine tuned model. Check the global leader board for merged llms:
https://huggingface.co/spaces/HuggingFaceH4/open_llm_leaderboard

|  Merge method   | Multiple models |
| :-------------: | :-------------: |
| Task Arithmetic |        ✅        |
|      Slerp      |        ❌        |
|    Ties/Dare    |        ✅        |
|   Passthrough   |        ✅        |

## Requirements For merging models
- Models must have the SAME architecture. You cant merge different architectures
- Models should have SAME number of layers.
- 

## Mode Architectures
- Llama
- Mistral
- Phi (?)

## Relevant Glossary
- Task Arithmetic: Allows you to manipulate model's weights using basic arithmetic. See [Editing Models with Task Arithmetic](https://arxiv.org/abs/2212.04089)
- Task vectors: A vector that describes weights from a model

## Relevant Videos
- https://www.youtube.com/watch?v=byf-y0P4hMg