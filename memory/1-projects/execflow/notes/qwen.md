# Qwen Deep Dive

## Why Qwen?

Qwen (通义千问) is Alibaba's LLM series. Strong performance, permissive license, good for commercial use.

## Available Models

| Model | Params | Context | Best For |
|-------|--------|---------|----------|
| Qwen2.5 | 0.5B - 72B | 128K | General purpose |
| Qwen2.5-Coder | 1.5B - 32B | 128K | Code generation |
| Qwen2.5-Math | 1.5B - 72B | 4K | Math reasoning |
| Qwen2-VL | 2B - 72B | 128K | Vision + language |
| QwQ-32B-Preview | 32B | 32K | Reasoning (o1-like) |

## Recommended for ExecFlow

### MVP (API)
- **Qwen2.5-7B** or **Qwen2.5-14B** via OpenRouter/Together
- Cheap, fast, good at instruction following
- ~$0.10-0.30 per 1M tokens

### Local Deployment (RTX 4090, 24GB)
- **Qwen2.5-7B** (Q4_K_M) — ~5GB VRAM, fast, good quality
- **Qwen2.5-14B** (Q4_K_M) — ~9GB VRAM, better reasoning
- **Qwen2.5-Coder-7B** — if users want coding help
- **QwQ-32B-Preview** (Q4_K_M) — ~20GB VRAM, best reasoning but slower

### Hardware Fit

| Model | Quant | VRAM | Tokens/sec (RTX 4090) |
|-------|-------|------|----------------------|
| Qwen2.5-7B | Q4_K_M | 5GB | ~80-100 t/s |
| Qwen2.5-14B | Q4_K_M | 9GB | ~50-60 t/s |
| Qwen2.5-32B | Q4_K_M | 20GB | ~25-30 t/s |
| QwQ-32B | Q4_K_M | 20GB | ~15-20 t/s |

## Licensing

- **Qwen2.5:** Apache 2.0 (commercial use OK)
- **Qwen2.5-Coder:** Apache 2.0
- **QwQ:** Apache 2.0

All safe for B2C SaaS.

## Deployment Options

### Option 1: vLLM (Recommended)
```bash
vllm serve Qwen/Qwen2.5-7B-Instruct --quantization awq
```
- Fastest inference
- OpenAI-compatible API
- Easy to swap models

### Option 2: llama.cpp
```bash
./llama-server -m qwen2.5-7b-q4_k_m.gguf
```
- Lower memory footprint
- Good for edge/single-GPU
- Native tool calling support

### Option 3: Ollama
```bash
ollama run qwen2.5:7b
```
- Easiest setup
- Good for prototyping
- Slightly slower than vLLM

## Comparison to Llama/Mistral

| Aspect | Qwen2.5 | Llama 3.1 | Mistral 7B |
|--------|---------|-----------|------------|
| Chinese | ⭐⭐⭐ Excellent | ⭐⭐ Good | ⭐⭐ Good |
| English | ⭐⭐⭐ Excellent | ⭐⭐⭐ Excellent | ⭐⭐⭐ Excellent |
| Coding | ⭐⭐⭐ Excellent | ⭐⭐⭐ Excellent | ⭐⭐ Good |
| Reasoning | ⭐⭐⭐ Excellent | ⭐⭐⭐ Excellent | ⭐⭐ Good |
| Context (128K) | ⭐⭐⭐ Yes | ⭐⭐⭐ Yes | ⭐⭐ 32K |
| Tool Use | ⭐⭐⭐ Native | ⭐⭐⭐ Native | ⭐⭐ Via prompt |

## Verdict

**Qwen2.5-7B or 14B is a solid choice.**
- Beats Llama 3.1 8B on many benchmarks
- Better Chinese support (if that's a market)
- Native tool calling (great for agents)
- Apache 2.0 license (no legal headaches)

For ExecFlow MVP: Start with **Qwen2.5-7B** on API, move to local **Qwen2.5-14B** when you have hardware.
