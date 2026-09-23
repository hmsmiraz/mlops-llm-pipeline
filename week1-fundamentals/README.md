# Week 1 — Ollama Model Comparison

Models compared: `llama3:8b` vs `mistral` (both run locally via Ollama, CPU-only, no GPU)

| Prompt                          | llama3:8b time | mistral time | Notes                                      |
|----------------------------------|----------------|--------------|---------------------------------------------|
| Explain container orchestrator  | 39.9s          | 47.5s        | Similar accuracy, llama3 more concise       |
| Reverse string function          | 48.4s          | 50.0s        | Nearly identical code + explanation         |
| Poem about debugging             | 44.4s          | 1m12.4s      | mistral much slower on creative/long output |

## Observations
- **Speed:** llama3:8b was faster on every prompt, and the gap widened on longer, more creative outputs.
- **Memory:** System peaked at ~13GB RAM used out of 15GB, with swap actively in use (2GB). Running an 8B model on CPU is memory-intensive even without a GPU.
- **Quality:** Both models gave technically correct, comparable answers for factual and coding prompts. For creative writing, mistral produced longer, more elaborate output at the cost of speed.
- **Takeaway:** On this CPU-only laptop, llama3:8b offers a better speed/quality tradeoff for general use; mistral may be worth it only where longer, more detailed output matters more than latency.
