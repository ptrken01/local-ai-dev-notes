# Qwen Prompt Examples for Local-First Teams

# Qwen Prompt Examples for Local-First Teams

In the world of local-first AI, speed, privacy, and reproducibility are non-negotiable. Teams building applications that rely on LLMs in resource-constrained or offline environments need reliable, optimized prompt templates to avoid common pitfalls like hallucinations, excessive token usage, or poor performance on smaller models.

The **MLX-Optimized Local-LLM Prompt Pack** is a collection of 48 production-ready prompts, fine-tuned for smaller local models (like Qwen, Llama, Mistral) and optimized for MLX-based inference. These prompts are ready to use in agent loops, with JSON output formats and copy-paste-ready templates — no extra setup required.

Below are real-world examples of how to use these prompts in a practical workflow.

---

## Example 1: Structured Output from Unstructured Text

**Prompt:**  
```text
You are a helpful assistant. Extract the following information from the text below:
- Name
- Email
- Phone Number

Return only valid JSON with no extra explanation or markdown formatting.

Text: John Doe, email john.doe@example.com, phone 123-456-7890.
```

**Expected Output:**  
```json
{
  "name": "John Doe",
  "email": "john.doe@example.com",
  "phone": "123-456-7890"
}
```

This prompt is optimized to reduce token waste and works reliably on smaller models with consistent output formatting. It’s ideal for parsing user input or data extraction tasks.

---

## Example 2: Multi-Turn Conversation Summarization

**Prompt:**  
```text
You are a helpful assistant. Summarize the following conversation in one sentence.

User: I need help with my account.
Assistant: Sure, what seems to be the issue?
User: My password isn't working.
Assistant: Let me reset it for you.

Summary:
```

**Expected Output:**  
```json
{
  "summary": "User needs help resetting their password."
}
```

This prompt works well in agent loops where context is passed between steps. It's designed to be robust and not require long prompts, which helps avoid token limits on local models.

---

## Example 3: Code Generation with Constraints

**Prompt:**  
```text
You are a Python developer. Generate a function that takes two integers and returns their sum.
The function must:
- Be named `add_numbers`
- Not use the `+` operator
- Use only basic arithmetic operations

Return only the code block, no explanation.

Example:
def add_numbers(a, b):
    return a - (-b)
```

**Expected Output:**  
```python
def add_numbers(a, b):
    return a - (-b)
```

This is great for local agents that need to generate or validate code snippets. The prompt forces specific constraints, which reduces hallucinations and increases reliability.

---

## Example 4: Sentiment Classification with Confidence

**Prompt:**  
```text
Classify the sentiment of the following sentence into one of three categories:
- Positive
- Negative
- Neutral

Return only a JSON object with the label and confidence score (0.0 to 1.0).

Sentence: I love this product, it's amazing!
```

**Expected Output:**  
```json
{
  "label": "Positive",
  "confidence": 0.95
}
```

This is useful for feedback analysis or content moderation workflows where you need both classification and certainty.

---

## Example 5: Task Prioritization

**Prompt:**  
```text
You are a task prioritizer. Given the following list of tasks, rank them from highest to lowest priority based on urgency and impact.
Return only a JSON array with task titles in order.

Tasks:
- Fix login bug
- Add dark mode
- Update documentation
- Refactor API endpoints

Priority ranking:
```

**Expected Output:**  
```json
[
  "Fix login bug",
  "Refactor API endpoints",
  "Add dark mode",
  "Update documentation"
]
```

This prompt is ideal for integrating into local workflows where tasks need to be dynamically prioritized.

---

## Example 6: JSON Schema Validation Prompt

**Prompt:**  
```text
Validate the following JSON against this schema:
{
  "type": "object",
  "properties": {
    "name": {"type": "string"},
    "age": {"type": "integer", "minimum": 0}
  },
  "required": ["name", "age"]
}

If valid, return true. If invalid, return false and explain the error.

JSON: {"name": "Alice", "age": 30}
```

**Expected Output:**  
```json
{
  "valid": true
}
```

This is great for ensuring data integrity in pipelines where local LLMs are used to validate user input or API responses.

---

## Example 7: Data Enrichment from a Knowledge Base

**Prompt:**  
```text
You are an assistant. Based on the following knowledge base entry, extract the key facts and summarize them.

Knowledge Base:
- Name: Qwen
- Type: Large Language Model
- Capabilities: Text generation, summarization, coding

Summary:
```

**Expected Output:**  
```json
{
  "summary": "Qwen is a large language model capable of text generation, summarization, and coding tasks."
}
```

This helps teams integrate local LLMs with knowledge bases or internal documentation to improve accuracy and relevance.

---

## Example 8: Multi-Step Reasoning with Step-by-Step Output

**Prompt:**  
```text
You are a logical reasoning assistant. Solve the following problem step by step:

If A is greater than B, and B is greater than C, then what can you conclude about A and C?

Return only valid JSON with a list of steps and the conclusion.

Problem: A > B > C
```

**Expected Output:**  
```json
{
  "steps": [
    "A > B",
    "B > C",
    "Therefore A > C"
  ],
  "conclusion": "A is greater than C."
}
```

This is useful for reasoning agents that need to explain their logic, especially in domains like education or compliance.

---

## Get it

Get the **MLX-Optimized Local-LLM Prompt Pack** with 48 production-ready prompts optimized for local inference. Copy-paste + JSON for agent loops.  
👉 [Download on Gumroad](https://ptrk-en.gumroad.com/l/mlx-prompt-pack)

## FAQ

- **In practice, what does 'Qwen Prompt Examples for Local-First Teams' actually cover?** This guide walks through qwen prompt examples for local-first teams with runnable steps you can apply on your own machine. Nothing here depends on a paid account or a cloud subscription - it is local-first by design. The focus is the parts that break in production, not the happy path you already know.
- **Is there a ready-to-use resource that goes deeper?** Yes - The MLX-Optimized Local-LLM Prompt Pack: 48 production prompts tuned for smaller local models — copy-paste + JSON for agent loops. It is a build-once pack ($19) with copy-paste assets, so you apply it immediately instead of re-deriving the fundamentals. Get it at https://ptrk-en.gumroad.com/l/mlx-prompt-pack.
- **Do I need any API keys or paid tools to follow along?** No. The approach is local-first: you run it on your own hardware with free, open tooling. There are no mandatory accounts, no usage-based billing, and nothing stops working if you cancel a subscription.
