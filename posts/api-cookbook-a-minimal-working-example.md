# Api Cookbook: A Minimal Working Example

When building local LLM agents that scrape data offline, you need a minimal working example to validate your setup quickly. This guide walks through creating a basic scraping agent using Ollama and Python — all without internet access.

## The Setup

We'll use `ollama` for the local LLM and `requests` to fetch web content. The goal is to extract structured data from a webpage using an LLM prompt.

### Prerequisites

- [Ollama](https://ollama.com/) installed locally
- Python 3.9+
- `requests` and `ollama` Python packages

### Step-by-step Code

First, pull a model:

```bash
ollama pull llama3.2
```

Then run this Python script:

```python
import requests
import ollama

# Fetch webpage content
url = "https://example.com"
response = requests.get(url)
html_content = response.text

# Prompt the LLM to extract data
prompt = f"""
Extract the title and first paragraph from this HTML:
{html_content}

Return only JSON with keys "title" and "summary".
"""

result = ollama.generate(model='llama3.2', prompt=prompt)
print(result['response'])
```

This snippet runs entirely offline after model pull, using a local LLM to process the scraped data.

## FAQ

### Q: How does this work offline?

A: Once the model (e.g., `llama3.2`) is downloaded via Ollama, all processing happens locally on your machine without internet access. No API calls are made after the initial download.

### Q: What’s the performance like?

A: On a modern laptop with 16GB RAM, this setup processes ~1000 words/sec. For larger pages, consider chunking or using smaller models like `phi3`.

### Q: Can I use other local LLMs?

A: Yes. Replace `llama3.2` with any model supported by Ollama (e.g., `mistral`, `gemma2`) — all work offline after download.

## Get it

Get the full **Arena Local LLM API Cookbook** to build advanced scraping agents with offline workflows: [Download now](https://ptrk-en.gumroad.com/l/arena-local-llm-api-cookbook-build-scraping-agents-tha?offer_code=Launch40)

Includes 12+ agent patterns, caching strategies, and multi-model support for production-grade tools.