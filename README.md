# Tech Tutor

Ask a technical question and get a clear, structured explanation from two LLMs: a cloud model (OpenAI) and a local model running on your own computer with Ollama. The cloud model then compares the two answers and points out mistakes.

## What it does

- **Explains code or concepts** in four sections: the answer in one sentence, a step-by-step breakdown, a runnable example, and common mistakes to watch out for
- **Adjusts to your level**: `beginner` (no jargon, everyday analogies), `intermediate` (how and why), `expert` (concise, edge cases, performance)
- **Streams the answers** in as they're written
- **Compares the answers**: the cloud model gives a verdict, quotes factual errors and lists anything both answers missed

## Why two models

A local model is free, private (your code never leaves your computer) and works offline, but small models make mistakes. A cloud model is more accurate but costs money per question. Running both side by side, with the comparison step at the end, shows how far a local model can be trusted.

Example: with the very small `gemma3:270m`, the local answer to a question about `yield from` and set comprehensions talked about "a compiler" and "vectors". The comparison step quoted it as incorrect.

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
4. Open `tech_tutor.ipynb`, type your question in the question cell and run all cells
