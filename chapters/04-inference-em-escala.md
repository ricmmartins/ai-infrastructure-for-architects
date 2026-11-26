# Chapter 4 — Inference at Scale

The secret of a good inference architecture is not having more GPUs. It is not wasting the GPUs you already have.

For the operational side of how to run and troubleshoot these engines in production, see [AI for Infra Pros](https://www.ai4infra.com/).

---

## The problem the architect needs to solve

LLM inference is not a normal request-response. It has two phases with completely different requirements. Ignoring that difference leads to expensive and underutilized clusters.

---

## Prefill vs Decode

![Prefill vs Decode](../assets/ch4-prefill-decode.svg)

| Phase      | What it does                       | Bottleneck       | Main impact           |
|------------|------------------------------------|------------------|-----------------------|
| Prefill    | Processes the entire prompt at once | Compute (FLOPs)  | Time to First Token (TTFT) |
| Decode     | Generates one token at a time      | Memory (KV cache) | Throughput (tokens/s) |

Long prompts overload Prefill. Long generations overload Decode and HBM. A single GPU pool rarely optimizes both phases at the same time.

---

## Continuous Batching

Instead of waiting for a full batch, the system dynamically adds and removes requests.

Benefit: higher GPU utilization. Trade-off: can increase the latency of individual requests if the batch becomes too large.

Continuous batching is almost always desirable in production. The limit is how much additional latency you accept.

---

## PagedAttention

Technique that treats the KV cache like memory pages (similar to virtual memory). It reduces HBM fragmentation and allows larger batches and longer contexts on the same GPU. It is the foundation of modern engines like vLLM.

Engines that implement PagedAttention (vLLM, SGLang) can serve more users per GPU than naive implementations.

---

## Disaggregated Serving

![Disaggregated Serving](../assets/ch4-disaggregated-serving.svg)

Separates GPUs into two pools: Prefill optimized for compute and Decode optimized for memory and throughput.

It is worth it when there is large variation in prompt size, need to optimize TTFT and throughput at the same time, and large clusters (dozens or hundreds of GPUs).

Complexity cost: needs to transfer the KV cache between pools and requires more sophisticated orchestration. It does not always pay off in small clusters.

---

## Speculative Decoding

Uses a smaller and faster model to guess several tokens, which are then verified by the large model. It can increase throughput by 1.5–2.5×. It adds complexity and latency on the critical path. It works better on certain types of generation.

Use when throughput is the bottleneck and you have the capacity to operate two models.

---

## Decision framework

| Priority                    | Recommended pattern               | Complexity   |
|-----------------------------|-----------------------------------|--------------|
| Lowest TTFT                 | Optimized Prefill + small batch   | Medium       |
| Highest throughput          | Continuous batching + PagedAttention | Low-Medium |
| Both (TTFT + throughput)    | Disaggregated Serving             | High         |
| Cost per token              | Quantization + aggressive batching | Medium      |
| Operational simplicity      | Managed engine (Foundry)          | Low          |

---

## When to use / when not to use

| Situation                             | Recommendation                        | What breaks if wrong                      |
|--------------------------------------|---------------------------------------|-------------------------------------------|
| Small cluster (< 8 GPUs)             | Continuous batching + PagedAttention  | Disaggregated: complexity without gain    |
| Large cluster + aggressive TTFT SLA  | Disaggregated Serving                 | Single pool: TTFT or utilization suffer   |
| Low volume                           | Managed service                       | Self-managed: operation without return    |
| Need for maximum throughput          | Speculative decoding + batching       | Without speculative: leaves throughput on the table |
| Team without inference experience     | Avoid disaggregated at the start      | Starting complex: operational debt        |

---

## Checklist

- [ ] Can I explain the difference between Prefill and Decode to a stakeholder?
- [ ] Do I understand why continuous batching increases utilization?
- [ ] Do I know when disaggregated serving is worth the complexity?
- [ ] Can I map these patterns to engines (vLLM, SGLang, Foundry)?
- [ ] Do I understand the impact of these choices on cost per million tokens?

---

Next: RAG and Retrieval Architectures. How retrieval enters the latency path and what the architect decides.
