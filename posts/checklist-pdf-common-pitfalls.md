# Checklist Pdf Common Pitfalls

When setting up a local LLM agent using tools like Ollama, MLX, or llama.cpp, the checklist PDF you download can be a lifesaver—or a source of frustration if not properly understood. Here’s how to avoid common pitfalls and build a fast, private, and repeatable workflow.

## Common Mistake: Skipping Environment Setup

One frequent error is assuming that `ollama run` or `llama.cpp` will work out-of-the-box without proper environment preparation. For example, Ollama requires a GPU with sufficient VRAM (e.g., 8GB for LLaMA-2 7B). You may also need to export CUDA paths manually if using NVIDIA GPUs.

```bash
export CUDA_VISIBLE_DEVICES=0
```

Always verify your system meets hardware requirements before downloading models. The checklist should include checking `nvidia-smi` and available RAM.

## Common Mistake: Misunderstanding Model Sizes

A second pitfall is choosing a model that's too large for your system. For instance, running LLaMA-2 70B on a machine with only 16GB VRAM will likely fail or cause excessive swapping. Use tools like `ollama list` to verify what’s installed and ensure compatibility.

```bash
ollama list
```

A better approach is starting small—try `llama3:8b` first, then scale up as needed.

## Common Mistake: Forgetting to Configure Memory Limits

Memory limits are often overlooked. When using llama.cpp, you must explicitly set how much memory to use:

```bash
./main -m models/llama-2-7b.gguf -c 2048 --temp 0.2
```

This ensures the model doesn’t crash due to memory overflow.

## Common Mistake: Ignoring Local Port Conflicts

If you're running multiple agents, port conflicts can cause failures. Ensure that each agent uses a unique port:

```bash
ollama serve --port 11435
```

Use `netstat -tuln | grep 11435` to check if the port is free before starting.

## Common Mistake: Not Verifying Model Integrity

A model file can become corrupted during download. Always validate checksums or use tools like:

```bash
sha256sum models/llama-2-7b.gguf
```

Check against official hashes to prevent silent failures.

## FAQ

**Q: What’s the fastest way to test if my local LLM is running correctly?**

A: Run a simple prompt using `ollama run` or `llama.cpp`. For example, type `ollama run llama3:8b "Hello"` to verify it responds. If there's no output, check logs or memory usage.

**Q: How do I know if my GPU supports the model I want?**

A: Check the model’s VRAM requirement and compare with your GPU's available memory using `nvidia-smi`. For instance, LLaMA-2 7B requires ~8GB; larger models need more.

**Q: Can I run multiple local agents without conflicts?**

A: Yes, by assigning different ports to each instance. Use `ollama serve --port 11435` and `ollama serve --port 11436`, ensuring no overlapping processes are active.

## Get it

Grab the **Arena Local LLM Agent Setup Checklist for Runners Using Ollama, MLX, and llama.cpp** to avoid these pitfalls. This checklist includes verified tool versions, memory recommendations, and port configurations for a smooth workflow. [Get it here](https://ptrk-en.gumroad.com/l/arena-local-llm-agent-setup-checklist-for-runners-usin?offer_code=Launch40).