# AI For Beginners: A Minimal Working Example

You don't need to be a data scientist or software engineer to use AI for real work. This guide shows you how to build a simple, private AI workflow that runs on your own machine—no coding required.

## The Setup

We'll create a minimal example using an open-source language model (LLM) and a simple Python script. This example works entirely offline, so your data stays private. It takes text input and returns AI-generated responses, all in one file.

```python
# ai_minimal.py
import os
from transformers import pipeline

# Initialize the model (downloads once)
model = pipeline("text-generation", 
                 model="distilgpt2",
                 device=-1)  # -1 for CPU, 0 for GPU

def ai_response(prompt):
    response = model(prompt, max_length=100, num_return_sequences=1)
    return response[0]['generated_text']

# Example usage
if __name__ == "__main__":
    user_input = "Write a short email about project status"
    result = ai_response(user_input)
    print(result)
```

Run it with:
```bash
python ai_minimal.py
```

This example uses the `distilgpt2` model, which is lightweight (around 240MB) and runs on any computer. The first run downloads the model; subsequent runs use your local copy.

## How It Works

The script initializes a text generation pipeline using Hugging Face's Transformers library. The `pipeline()` function handles all complexity: tokenization, model loading, and inference. When you call `ai_response()`, it processes your prompt through the model and returns a generated response.

This workflow is "build-once": after downloading the model once, it works offline. Your prompts and responses never leave your machine—no cloud API calls required.

## FAQ

**Q: How much RAM does this need?**
A: The `distilgpt2` model requires about 2-3GB RAM for generation. It runs on any modern laptop or desktop with at least 4GB RAM, and you can adjust `max_length` to reduce memory usage.

**Q: Can I use my own data?**
A: Yes. You can modify the script to load prompts from files or databases. The pipeline accepts any text input, so your personal documents, emails, or notes can serve as training material for custom instructions.

**Q: How long does it take to generate responses?**
A: On a modern CPU, expect 5-10 seconds per response. GPU acceleration reduces this to under 2 seconds. For batch processing, you can modify the script to handle multiple inputs simultaneously.

## Your Workflow

This minimal example becomes your foundation for productivity workflows:

1. **Prompt Engineering**: Write clear instructions like "Summarize this meeting transcript in bullet points."
2. **Private Processing**: All work happens locally—no data leaks.
3. **Reusability**: Save your prompts as templates. Reuse them across projects without retraining.

You can extend this script to:
- Process multiple files
- Add logging for tracking outputs
- Integrate with your existing tools (like Notion or Excel)

## Get It

Ready to build your private AI workflow? The **AI Skills eBook** gives you 10+ real-world examples, no-code templates, and step-by-step instructions. Download it now: [https://ptrk-en.gumroad.com/l/ai-skills-ebook?offer_code=Launch40](https://ptrk-en.gumroad.com/l/ai-skills-ebook?offer_code=Launch40)

This guide is part of the *2026 Productivity Guide*—a plain-English resource for professionals who want faster, private, and build-once AI workflows. No jargon, just practical results.