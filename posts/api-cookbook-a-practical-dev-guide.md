# Api Cookbook: A Practical Dev Guide

Building scraping agents that work offline with local LLMs is both practical and powerful — especially when you're processing sensitive data or working in constrained environments. This guide shows how to create robust, private scraping workflows using Ollama and Python.

## The Setup

We'll use `ollama` as our local LLM backend, `requests` for HTTP calls, and `BeautifulSoup` for parsing HTML. All code runs locally, with no cloud dependencies.

### Step 1: Start Ollama
First, ensure Ollama is running:

```bash
ollama serve
```

Then pull a model (e.g., `llama3`):

```bash
ollama pull llama3
```

### Step 2: Python Script for Scraping + LLM Parsing

Here's a minimal, runnable example that scrapes a page and uses the local LLM to extract structured data:

```python
import requests
from bs4 import BeautifulSoup
import json
import ollama

def scrape_and_parse(url):
    response = requests.get(url)
    soup = BeautifulSoup(response.text, 'html.parser')

    # Extract raw text
    content = soup.get_text()

    prompt = f"""
    Extract the following data from the text below:
    - Product name
    - Price
    - Description

    Text: {content[:1000]}
    """

    response = ollama.generate(model='llama3', prompt=prompt)
    return json.loads(response['response'])

# Usage
result = scrape_and_parse("https://example.com/product")
print(result)
```

This script runs in under 5 seconds locally, and the LLM output is parsed into structured JSON. No internet required after the initial model pull.

## Key Benefits

- **Offline First**: Everything runs on your machine.
- **Private by Default**: No data leaves your system.
- **Reproducible Workflows**: Same code, same results across environments.
- **Fast Iteration**: Local LLMs offer sub-second response times for basic tasks.

## FAQ

**Q: How much RAM does Ollama need to run efficiently?**

A: For models like `llama3`, 8GB RAM is the minimum recommended. On a Mac with M1/M2 chips, 16GB works well and allows concurrent use of other tools.

**Q: Can I use this for large-scale scraping projects?**

A: Yes, but consider batching requests to avoid overwhelming your local machine. You can integrate `concurrent.futures` or task queues like Celery if needed.

**Q: What's the latency difference between local and cloud APIs?**

A: Local LLMs typically respond in 1–3 seconds for simple queries. Cloud APIs can be faster (0.5–1.5s), but they’re not private, and data is sent over the internet.

## Get it

Get the full Api Cookbook with more patterns, advanced parsing, and workflow automation [here](https://ptrk-en.gumroad.com/l/arena-local-llm-api-cookbook-build-scraping-agents-tha?offer_code=Launch40). It includes ready-to-use agents for e-commerce, news, and research scraping.