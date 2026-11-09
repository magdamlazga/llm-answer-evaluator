# LLM Answer Evaluator

Ask a technical question and get a clear, structured explanation from two LLMs: a cloud model (OpenAI) and a local model running on your own computer with Ollama. The cloud model then acts as a judge: it compares the two answers, quotes factual errors and says which answer to trust.

## What it does

- **Explains code or concepts** in four sections: the answer in one sentence, a step-by-step breakdown, a runnable example, and common mistakes to watch out for
- **Adjusts to your level**: `beginner` (no jargon, everyday analogies), `intermediate` (how and why), `expert` (concise, edge cases, performance)
- **Streams the answers** in as they're written
- **Compares the answers**: the cloud model gives a verdict, quotes factual errors and lists anything both answers missed

## Why it matters, and for whom

LLMs are now part of everyday technical work: developers paste code into them, learners use them as tutors, teams build them into products. The weak spot is that **an LLM sounds equally confident when it's wrong**. A fluent but incorrect explanation is worse than no explanation, because the reader has no reason to doubt it.

So the main question is not just "what did the model answer?" but **"how good is this answer, and can I trust this model?"** This project answers both at once: it explains, and it evaluates the explanation.

### Evaluating LLM answers

The comparison step uses a pattern called **LLM-as-a-judge**: a stronger model reviews the output of another model against clear criteria. Here it gives:

- **Verdict:** which answer is more correct and more useful
- **Mistakes:** factual errors, quoted word for word, so a human can check them
- **Missing:** important points that both answers left out

Comparing models on the **same question with the same system prompt** makes the comparison fair: any difference comes from the model, not from the instructions.

This turns a vague feeling ("the local model seems worse") into evidence. Example: with the very small `gemma3:270m`, the local answer to a question about `yield from` and set comprehensions talked about "a compiler" and "vectors". The judge quoted both phrases as incorrect, while the cloud answer was confirmed as accurate.

### The trade-off it makes visible

| | Local model (Ollama) | Cloud model (OpenAI) |
|---|---|---|
| Cost | Free after download | Paid per request |
| Privacy | Code never leaves your computer | Sent to an external API |
| Works offline | Yes | No |
| Quality | Depends heavily on model size | Usually higher |

Teams often want a local model for cost or privacy reasons, but need proof that it's good enough. Running both side by side shows where the local model can be trusted and where it can't.

### Who it's for

- **Learners and developers** who want explanations of code at their own level, with a built-in check that flags answers not to trust
- **Teams choosing between local and cloud models**, who need evidence of the quality difference before moving to a cheaper or more private option
- **AI and QA engineers** working on LLM quality, as a small, readable example of comparing models and using an LLM as a judge
- **Anyone building LLM features into a product**, since every such feature needs a way to check answers before users see them

### Limits of the evaluation

LLM-as-a-judge is useful but not neutral, and this proof of concept is honest about that:

- **Self-preference:** the judge is the same cloud model that wrote Answer A, and models tend to favour their own style
- **Position bias:** the cloud answer is always shown first, and judges can favour the first answer they read
- **One question is not a benchmark:** a single comparison shows a difference, but doesn't measure it reliably

Ways to make it stronger: use a different model as the judge, shuffle and hide which model wrote which answer, score each answer from 1 to 5 against a fixed rubric (correctness, clarity, completeness), and run it on a fixed set of test questions to compare models with real numbers.

## How it works

Ollama provides the same API as OpenAI, so one client library talks to both models. Only the address changes:

```python
cloud = OpenAI()
local = OpenAI(base_url="http://localhost:11434/v1", api_key="ollama")
```

Each question goes to both models with the same system prompt, built from the chosen level. A third call sends the question and both answers to the cloud model for review.

## Setup

1. Install the Python packages: `pip install -r requirements.txt`
2. Copy `.env.example` to `.env` and add your OpenAI API key
3. Install [Ollama](https://ollama.com) and download a model, for example `ollama pull llama3.2`. Set `MODEL_LOCAL` in the notebook to match.
4. Open `llm_answer_evaluator.ipynb`, type your question in the question cell and run all cells
