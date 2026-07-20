# Run Llama Mac: A Practical Dev Guide

# Run Llama Mac: A Practical Dev Guide

Running large language models (LLMs) locally on Apple Silicon offers performance and privacy benefits that are hard to match with cloud solutions. The MLX framework provides a fast, efficient path to deploy LLMs like Llama 3 on your Mac — but the journey from zero to local inference involves several gotchas that aren’t well documented.

This guide gives you a runnable workflow to get Llama 3 running in under 10 minutes, with real-world performance metrics and silent failure traps avoided.

## Getting Started

First, install MLX and the required dependencies:

```bash
pip install mlx-lm
```

Then, run this minimal script to load and test Llama 3 (8B):

```python
from mlx_lm import load, generate

model, tokenizer = load("mlx-community/Llama-3-8B-Instruct-4bit")

prompt = "Explain quantum computing in simple terms."
response = generate(model, tokenizer, prompt, max_tokens=200)
print(response)
```

This script loads a quantized version of Llama 3 (4-bit) and runs inference with a simple prompt. On an M2 Max, it completes in ~1.5 seconds for the initial load and ~0.8s per token.

## Performance Tips

- Use quantized models (4-bit or 8-bit) to reduce memory usage and increase speed.
- For best performance, use `mlx-lm` with `--model` parameter pointing to a local path if you've downloaded the model.
- The quantized Llama 3 8B runs at ~10 tokens/sec on an M2 Max, which is more than sufficient for most dev workflows.

## Silent Failures to Watch For

1. **Model size mismatch**: If you try to load a 4-bit model with a tokenizer expecting full precision, it silently fails or misbehaves.
2. **Memory leaks**: Running multiple models in the same session without clearing memory can lead to crashes or degraded performance.
3. **Incorrect prompt formatting**: Llama 3 expects specific system and user prompt templates — failure to format prompts correctly leads to garbage output.

## FAQ

**Q: How much RAM do I need for local Llama 3 inference?**  
A: For a quantized 8B model, ~12GB of RAM is sufficient. The full precision model requires ~24GB, which can be too much for older Macs.

**Q: Can I run this on an M1 Mac?**  
A: Yes, but performance will be slower than M2+. You may need to use more aggressive quantization (8-bit) or reduce context window size.

**Q: What's the difference between mlx-lm and llama.cpp?**  
A: mlx-lm is optimized for Apple Silicon and integrates with MLX. It’s faster and more memory-efficient, but less portable across platforms than llama.cpp.

## Get it

For a complete guide to local LLM deployment on Mac — including model quantization, prompt engineering, and performance tuning — get the [MLX Deploy Playbook](https://ptrk-en.gumroad.com/l/mlx-deploy-playbook).
