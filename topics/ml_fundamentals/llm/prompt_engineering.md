#llm #promptengineering

# Prompt Engineering

## 1. Overview

Prompt engineering is the practice of designing inputs to LLMs to elicit desired outputs **without modifying model weights**. It exploits the model's in-context learning ability — the capacity to adapt behaviour based on instructions and examples in the prompt.

## 2. Core Techniques

### 2.1 Zero-Shot Prompting
Give the model a task description with no examples:
```
Classify the sentiment of this review as positive or negative:
"The food was terrible and the service was slow."
→ Negative
```
- Relies entirely on pre-trained knowledge
- Works well for tasks the model encountered during training

### 2.2 Few-Shot Prompting
Provide a small number of input-output examples before the actual query:
```
Review: "Amazing experience!" → Positive
Review: "Worst meal ever." → Negative
Review: "The ambiance was nice but food was cold." →
```
- Dramatically improves performance on novel or ambiguous tasks
- Performance scales with number of examples (up to a point, limited by context window)
- Example selection and ordering matter — choose diverse, representative examples

> **Source**: Brown et al., "Language Models are Few-Shot Learners" (GPT-3, 2020)

### 2.3 Chain-of-Thought (CoT)
Ask the model to show its reasoning step by step before giving the final answer:
```
Q: If a store has 5 apples and sells 2, then receives 8 more, how many apples does it have?
A: Let's think step by step. Start with 5 apples. Sell 2: 5 - 2 = 3. Receive 8: 3 + 8 = 11. The answer is 11.
```
- Significantly improves performance on arithmetic, logic, and multi-step reasoning
- Can be triggered with "Let's think step by step" (zero-shot CoT) or by providing worked examples (few-shot CoT)
- Most effective for larger models (>~100B parameters); smaller models may produce plausible-sounding but wrong reasoning

> **Source**: Wei et al., "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models" (2022)

### 2.4 Self-Consistency
Run CoT multiple times with temperature > 0, then **majority vote** on the final answer:
1. Sample $N$ reasoning paths (each may take different steps)
2. Extract the final answer from each path
3. Return the most common answer

- Improves over single CoT by marginalising over different reasoning paths
- More robust to individual reasoning errors
- Trade-off: $N\times$ the inference cost

> **Source**: Wang et al., "Self-Consistency Improves Chain of Thought Reasoning in Language Models" (2023)

### 2.5 ReAct (Reasoning + Acting)
Interleave reasoning traces with external tool use (search, calculator, API calls):
```
Thought: I need to find the population of France.
Action: Search("population of France 2024")
Observation: 68.4 million
Thought: Now I can answer the question.
Answer: The population of France is approximately 68.4 million.
```
- Grounds the model's reasoning in real-world information
- Reduces hallucination by verifying facts through tools
- Foundation for LLM agent architectures

> **Source**: Yao et al., "ReAct: Synergizing Reasoning and Acting in Language Models" (2023)

### 2.6 Tree of Thoughts (ToT)
Generalises CoT by exploring multiple reasoning branches:
1. Generate several possible next steps at each point
2. Evaluate which branches are promising (using the LLM itself or a heuristic)
3. Explore the best branches (BFS or DFS)
4. Backtrack from dead ends

- Useful for tasks requiring exploration and planning (e.g., puzzles, creative writing, game-playing)
- Significantly more expensive than CoT (many more LLM calls)

> **Source**: Yao et al., "Tree of Thoughts: Deliberate Problem Solving with Large Language Models" (2023)

## 3. Practical Guidelines

| Guideline | Why |
|:---------:|:---:|
| Be specific and explicit | Ambiguity leads to unpredictable outputs |
| Use delimiters for input data | Prevents prompt injection; clarifies structure (e.g., triple backticks, XML tags) |
| Specify output format | JSON, bullet points, table — the model follows format instructions well |
| Provide role/persona | "You are an expert ML engineer" focuses the response distribution |
| Constrain length | "Answer in 2-3 sentences" prevents verbose or truncated outputs |
| Order few-shot examples thoughtfully | Recency bias: the model pays more attention to later examples |

## 4. Common Failure Modes

- **Hallucination**: model generates plausible but factually wrong information. Mitigate with RAG, tool use, or self-verification prompts
- **Prompt injection**: adversarial input overrides system instructions. Mitigate with input sanitisation and delimiters
- **Lost in the middle**: for long contexts, models attend more to the beginning and end, losing information in the middle. Place important information at the start or end of the prompt
- **Sycophancy**: model agrees with the user even when wrong. Mitigate by asking it to critique its own answer

> **Source**: Liu et al., "Lost in the Middle: How Language Models Use Long Contexts" (2023)

## 5. Common Interview Questions

- **Q: When would you use few-shot vs fine-tuning?** Few-shot when you have limited data (<100 examples), need rapid iteration, or the task is straightforward. Fine-tuning when you have substantial data, need consistent behaviour, or the task requires specialised knowledge not in the base model.

- **Q: Why does chain-of-thought work?** It forces the model to decompose complex problems into simpler sub-problems, allocating more computation per step. The intermediate tokens act as "working memory", allowing the model to track state through a multi-step computation that wouldn't fit in a single forward pass.

- **Q: How do you evaluate prompt quality?** Define clear metrics (accuracy, format compliance, latency). Test on a held-out set of diverse inputs. Compare multiple prompt variants systematically. Track failure modes and edge cases. Automated evals (LLM-as-judge) can scale this.
