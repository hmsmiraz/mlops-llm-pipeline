# Week 2 — llama.cpp: Quantization Benchmark

Built llama.cpp from source and compared GGUF quantization levels directly,
bypassing Ollama's abstraction. Used Qwen2.5-0.5B-Instruct (a small model,
chosen to keep downloads light — ~400-500MB per quant vs several GB for 8B
models) to isolate the effect of quantization level itself.

## llama-bench results (synthetic benchmark)

| Quant  | Size       | Prompt Processing (t/s) | Token Generation (t/s) |
|--------|------------|--------------------------|--------------------------|
| Q4_K_M | 373.71 MiB | 105.15 ± 2.91             | 30.24 ± 1.34              |
| Q5_K_M | 394.95 MiB | 76.11 ± 1.34              | 28.50 ± 0.89              |
| Q8_0   | 500.79 MiB | 124.25 ± 11.23            | 28.19 ± 0.39              |

## Live generation (same prompt, real run)

| Quant  | Prompt t/s | Generation t/s |
|--------|------------|------------------|
| Q4_K_M | 66.5       | 27.0             |
| Q5_K_M | 68.6       | 25.8             |
| Q8_0   | 105.6      | 25.0             |

## Observations
- **Generation speed barely changes across quant levels** (27 → 25.8 → 25 t/s).
  On this small model, token generation appears memory-bandwidth-bound rather
  than compute-bound, so higher precision cost almost nothing in speed.
- **Prompt processing was fastest at Q8_0**, which is counter-intuitive — larger
  size didn't mean slower. Likely explanation: Q8_0 is a simpler, more
  SIMD-friendly quant format than the K-quant formats (Q4_K_M, Q5_K_M), so it
  processes faster per-token despite being larger on disk.
- **Output quality** was consistent and coherent across all three quant levels
  for this prompt — no obvious degradation at Q4_K_M, though this is a small
  0.5B model, so quality ceiling is lower than the llama3:8b used in Week 1
  regardless of quantization.
- **Takeaway:** for CPU-only serving on small models, Q8_0 is a reasonable
  default — comparable quality, no generation-speed penalty, and actually
  faster prompt processing than the "smaller" K-quants in this test.

## What I learned vs. Week 1 (Ollama)
Ollama hides all of this: quant level, model format, and raw inference flags.
Running llama.cpp directly surfaces the actual tradeoffs engineers make when
choosing a quantization level for production serving — and shows those
tradeoffs aren't always intuitive (see prompt-processing speed above).
