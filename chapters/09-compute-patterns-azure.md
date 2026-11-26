# Chapter 9 — Compute Patterns on Azure

The choice of compute pattern defines how much control and how much operation you will carry for the next 12 months. Getting this wrong is expensive to undo.

---

## The three patterns

![Compute Patterns on Azure for AI](../assets/ch9-compute-patterns.svg)

| Pattern                 | Best for                        | Control | Operations | When it breaks                         |
|-------------------------|---------------------------------|---------|------------|----------------------------------------|
| Managed (Foundry)       | Speed and small team           | Low     | Low        | High volume or fine inference control  |
| AKS + Engine            | Control/operations balance     | High    | Medium     | Team without Kubernetes/GPU maturity   |
| HPC (CycleCloud/Slurm)  | Large-scale training           | Very high| High     | Online inference or short jobs         |

Most teams start managed and only migrate when the cost per token or the requirement for inference customization starts to hurt.

---

## ND-series: what actually matters

ND-series VMs (H100, H200, B200) are the foundation. The architect needs to know three things:

- Amount of HBM per GPU (defines whether the model fits without aggressive quantization).
- Presence of NVLink (scale-up inside the node).
- InfiniBand option (scale-out between nodes).

InfiniBand is almost always waste for inference. It is mandatory for distributed training of large models. Choosing the wrong SKU here locks the design for months.

---

## AKS + vLLM / SGLang

This is the pattern when you need continuous batching, PagedAttention and quantization control without building an HPC cluster.

Decisions that change the outcome:

- Node pool size and autoscaling strategy (GPUs take time to come up).
- How to roll out a new model without dropping in-flight requests.
- Observability of GPU utilization and KV cache hit rate.

Without this, the cluster becomes expensive and underutilized. With this, you can achieve cost per token competitive with Foundry at high volume.

---

## CycleCloud / Slurm

Serves jobs that last hours or days and need gang scheduling and InfiniBand. It does not serve online inference. The operational overhead is high and is only justified when the training job cannot restart from zero.

---

## Decision framework

![Compute Pattern Decision](../assets/ch9-decision-compute.svg)

Ask in this order:

1. Is the main workload training or inference/agents?
2. How much control over the inference engine do you really need?
3. Does the team operate Kubernetes or Slurm today?
4. Does the current or projected volume justify the complexity?

If the answer to 3 is no and the volume is still low, stay managed. Migrating later is possible. Starting self-managed without operational capacity creates debt quickly.

---

## When to use / when not to use

| Situation                             | Recommended pattern                   | Main reason                       |
|--------------------------------------|---------------------------------------|-----------------------------------|
| Start fast, small team               | Foundry                               | Delivery speed                    |
| Inference at scale with control      | AKS + vLLM/SGLang                     | Utilization and cost per token    |
| Large model training                 | CycleCloud / Slurm                    | InfiniBand + scheduling           |
| Mix of inference and training        | Separate environments                 | Different load profiles           |
| Team without GPU experience          | Managed                               | Operational risk                  |

---

## Checklist

- [ ] Do I know the practical difference between the three patterns and when each one breaks?
- [ ] Can I justify AKS + vLLM vs Foundry with volume or control requirements?
- [ ] Do I understand why InfiniBand is almost never worth it for inference?
- [ ] Do I have a criterion not to start self-managed without operational capacity?
- [ ] Can I explain the choice to a stakeholder without listing services?

---

Next chapter: Networking, Storage and Data Patterns. Network and data stop being a detail and start affecting training time, RAG latency and scale-out viability.
