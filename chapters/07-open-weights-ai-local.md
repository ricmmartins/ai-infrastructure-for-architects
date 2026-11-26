# Chapter 7 — Open Weights and Local AI
## When self-hosting makes sense and how to design hybrid architectures

> “Open weights are not automatically cheaper. They are more controllable. The cost depends on how you operate them.”

---

## What Open Weights are

Open weights are models whose weights are publicly available (Llama, Mistral, Gemma, Phi, etc.). You can download them, run them locally or on your infrastructure and, in many cases, fine-tune them.

For the architect, the question is not “are open weights better?”. The question is:

**In which scenarios does the additional control justify the operational complexity?**

---

## When Open Weights make sense

![When to use Open Weights](../assets/ch7-decision-open-weights.svg)

| Factor                       | Favors Open Weights                | Favors Closed API             |
|------------------------------|------------------------------------|-------------------------------|
| Data residency / compliance  | Data cannot leave                  | Data can go to the API        |
| Very high volume             | Lower cost per token at scale      | Low or medium volume          |
| Need for fine-tuning         | You control the model              | Limited fine-tuning           |
| Extremely low latency        | Can run closer to the user         | Depends on API region         |
| Team and operations          | Has capacity to operate GPUs       | Wants to minimize operations  |
| Time-to-market               | Slower at the start                | Much faster                   |

---

## Hybrid Architecture

Most mature companies do not choose one or the other. They use both.

![Hybrid Architecture: Closed + Open Weights](../assets/ch7-hybrid-architecture.svg)

**Common pattern:**
- Closed model (Foundry / Azure OpenAI) for complex or high-quality tasks
- Self-hosted open weights for high-volume tasks, sensitive data or cost optimization
- A router (or policy) decides which path to use

---

## Engines and quantization

| Engine       | Best for                     | Note                               |
|--------------|------------------------------|------------------------------------|
| vLLM         | High throughput              | PagedAttention, widely used        |
| SGLang       | Low latency + structured     | Good for agents                    |
| Ollama       | Local development            | Simple, not for high production    |
| llama.cpp    | CPU / edge                   | Aggressive quantization            |

**Quantization** reduces model size and HBM consumption, allowing larger models to run on fewer GPUs — with some quality trade-off.

---

## Architect decisions

1. **What percentage of traffic can go to open weights?**
2. **Where to run?** (AKS + GPU, VMs, Foundry Model Catalog)
3. **How to version and update models?**
4. **What fallback strategy** if the local model fails?
5. **How to measure quality** compared to the closed model?

---

## When to use / when not to use

| Situation                             | Recommendation                        |
|--------------------------------------|---------------------------------------|
| Highly sensitive data                | Open weights                          |
| Low volume, need to deliver fast     | Closed API                            |
| High volume + mixed data             | Hybrid architecture                   |
| Frequent fine-tuning need            | Open weights                          |
| Small team without GPU experience    | Start with API, evolve later          |

---

## Chapter checklist

- [ ] Can I explain when open weights are an architecture decision and not just a cost decision?
- [ ] Do I understand the hybrid architecture pattern?
- [ ] Do I know the main engines and when to use each one?
- [ ] Do I have a framework to decide between API and self-hosted?
- [ ] Can I map this to Azure (Foundry + AKS/ND-series)?

---

## What comes next

With Chapter 7 we close **Part II — Core Patterns**.  
In Chapter 8 we enter **Part III — Azure Architecture Patterns**, starting with **Microsoft Foundry and agent patterns**.
