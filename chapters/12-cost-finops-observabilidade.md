# Chapter 12 — Cost, FinOps and Observability

In AI cost is not a consequence. It is a design variable. Decisions about model, context, steps and utilization change cost by orders of magnitude.

---

## Main drivers

![Main Cost Drivers in AI](../assets/ch12-cost-drivers.svg)

| Driver              | What it influences                        | Architect lever                        |
|---------------------|-------------------------------------------|----------------------------------------|
| Compute (GPU)       | Utilization, GPU type, usage time         | Batching, quantization, right-sizing   |
| Tokens              | Input/output size, agent steps            | Limit context and steps                |
| Data & Storage      | Training data volume, vector DB           | Lifecycle, retention                   |
| Network             | Egress, data transfer                     | Co-location, Private Link              |

---

## Cost model by mode

| Mode          | Main cost unit                 | How to control                           |
|---------------|--------------------------------|------------------------------------------|
| Training      | GPU-hours                      | Job duration, parallelism efficiency     |
| Inference     | Cost per million tokens        | Model, quantization, batching            |
| Agents        | Tokens × steps                 | Step limit, tool choice                  |
| RAG           | Retrieval + tokens             | Index quality, reranking                 |

---

## Observability

![Observability Layers for AI](../assets/ch12-observability-layers.svg)

Three levels:

1. Infrastructure — GPU utilization, latency, errors.
2. Model — tokens per request, TTFT, quality.
3. Agent / Application — number of steps, tool calls, cost per execution.

Without the third level you do not know why an agent's cost exploded.

---

## FinOps practices

Consistent tagging gives visibility by team or product. Budgets and alerts avoid surprises. GPU right-sizing reduces waste. Step limits on agents control amplification. Periodic model evaluation allows switching to a cheaper option when quality accepts it.

---

## When to use / when not to use

| Situation                             | Approach                              | What breaks if you do not              |
|--------------------------------------|---------------------------------------|----------------------------------------|
| High volume inference                | Optimize utilization and quantization | Pays for idle GPU or larger model than needed |
| Agents in production                 | Strict step limits + observability    | Cost per execution without ceiling     |
| Frequent training                    | Monitor GPU-hours closely             | Long jobs without visibility           |
| Pilot project                        | Accept higher cost at the start       | Optimizing too early delays delivery   |
| Need for predictability              | Managed models + limits               | Self-managed without capacity: surprise |

---

## Checklist

- [ ] Can I identify the main cost drivers of an AI system?
- [ ] Do I know the difference in cost model between training, inference and agents?
- [ ] Do I understand why agent observability is different from traditional observability?
- [ ] Do I have FinOps practices applicable to AI workloads?
- [ ] Can I estimate the impact of an architecture decision on cost?

---

Next: Security, Privacy and Compliance. Where sensitive data and agents with side-effects change the design.
