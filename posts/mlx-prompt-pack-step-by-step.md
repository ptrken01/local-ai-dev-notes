# Mlx Prompt Pack Step-by-Step

# Mlx Prompt Pack Step-by-Step

If you're building local LLM applications with MLX, you know how quickly prompt engineering can become a bottleneck. The Mlx Prompt Pack addresses this by providing 48 production-ready prompts optimized for smaller local models — all ready to copy-paste or use in JSON format for agent loops.

Let's walk through how to use it effectively in your workflow.

## Getting Started

First, download the pack from [Gumroad](https://ptrk-en.gumroad.com/l/mlx-prompt-pack). You'll get a zip file containing:

- A `prompts.json` file with all 48 prompts
- Example Python scripts for integration
- A README explaining usage patterns

Each prompt is tuned specifically for MLX-optimized models like Llama3, Mistral, and Phi-3, so they work efficiently on local hardware without overfitting.

## Using the JSON Prompts

The main file `prompts.json` contains all prompts in a structured format. Here's a sample structure:

```json
{
  "summarize": {
    "system": "You are a helpful assistant that summarizes text concisely.",
    "user": "Summarize: {input}"
  },
  "code_explain": {
    "system": "Explain code in simple terms.",
    "user": "Explain this code:\n{code}"
  }
}
```

Each prompt has two keys: `system` and `user`. These are designed to be used directly in your model calls.

## Example Workflow

Let’s say you're building a simple agent that summarizes text using the local LLM. Here’s how you’d integrate one of these prompts:

```python
import json
from mlx_lm import load, generate

# Load the model (example with Llama3)
model, tokenizer = load("mlx-community/Llama-3-8B-Instruct-4bit")

# Load prompts
with open("prompts.json") as f:
    prompts = json.load(f)

# Get prompt values
prompt_template = prompts["summarize"]
system_prompt = prompt_template["system"]
user_prompt = prompt_template["user"]

# Prepare input
input_text = "Machine learning is a subset of artificial intelligence that focuses on algorithms enabling computers to learn from and make predictions based on data."
final_prompt = f"{system_prompt}\n\n{user_prompt.format(input=input_text)}"

# Generate response
response = generate(model, tokenizer, final_prompt, max_tokens=100)
print(response)
```

This gives you a baseline that works with your local model. You can easily swap out prompts by changing the key in `prompts["summarize"]`.

## Agent Loops

For more advanced workflows, such as agent loops, it's easier to use the JSON directly. Here’s how to set up a simple loop using the `code_explain` prompt:

```python
def run_agent_loop(code_snippet):
    model, tokenizer = load("mlx-community/Mistral-7B-v0.2-4bit")
    
    with open("prompts.json") as f:
        prompts = json.load(f)
        
    explain_prompt = prompts["code_explain"]
    system = explain_prompt["system"]
    user_template = explain_prompt["user"]
    
    loop_prompt = f"{system}\n\n{user_template.format(code=code_snippet)}"
    
    response = generate(model, tokenizer, loop_prompt, max_tokens=150)
    return response

# Example usage
code = """
def fibonacci(n):
    if n <= 1:
        return n
    else:
        return fibonacci(n-1) + fibonacci(n-2)
"""
result = run_agent_loop(code)
print(result)
```

This example demonstrates how to build reusable components with the prompt pack. The key is to load the JSON once and reuse the prompts across multiple calls.

## Optimizing for Smaller Models

The prompts in this pack are specifically tuned for models under 10GB, such as:

- Llama3 8B
- Mistral 7B
- Phi-3 Mini

They avoid long instruction chains or overly complex formatting that might cause tokenization issues or performance degradation. Each prompt is tested to ensure it works within typical local inference constraints.

## Batch Processing

For batch operations, you can build a small utility function:

```python
def batch_summarize(texts):
    model, tokenizer = load("mlx-community/Llama-3-8B-Instruct-4bit")
    
    with open("prompts.json") as f:
        prompts = json.load(f)
        
    summarize_prompt = prompts["summarize"]
    system = summarize_prompt["system"]
    user_template = summarize_prompt["user"]
    
    results = []
    for text in texts:
        prompt = f"{system}\n\n{user_template.format(input=text)}"
        response = generate(model, tokenizer, prompt, max_tokens=100)
        results.append(response)
        
    return results
```

This allows you to process multiple inputs without reloading the model or prompts.

## Tips for Local Development

1. **Use 4-bit quantization**: All models in this pack are optimized with 4-bit quantization for faster inference and reduced memory usage.
2. **Preload the prompt JSON**: Loading it once at startup avoids redundant I/O.
3. **Adjust `max_tokens`**: Depending on your input length, tweak `max_tokens` to balance quality and performance.

## Get it

[Get the Mlx Prompt Pack](https://ptrk-en.gumroad.com/l/mlx-prompt-pack) – 48 optimized prompts for local LLMs, copy-paste + JSON ready for agent loops.

## FAQ

- **If I read 'Mlx Prompt Pack Step-by-Step' actually cover?** This guide walks through mlx prompt pack step-by-step with runnable steps you can apply on your own machine. Nothing here depends on a paid account or a cloud subscription - it is local-first by design. The focus is the parts that break in production, not the happy path you already know.
- **Is there a ready-to-use resource that goes deeper?** Yes - The MLX-Optimized Local-LLM Prompt Pack: 48 production prompts tuned for smaller local models — copy-paste + JSON for agent loops. It is a build-once pack ($19) with copy-paste assets, so you apply it immediately instead of re-deriving the fundamentals. Get it at https://ptrk-en.gumroad.com/l/mlx-prompt-pack.
- **Do I need any API keys or paid tools to follow along?** No. The approach is local-first: you run it on your own hardware with free, open tooling. There are no mandatory accounts, no usage-based billing, and nothing stops working if you cancel a subscription.
