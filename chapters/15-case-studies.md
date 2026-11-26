# Chapter 15 — Case Studies

Patterns only make sense when you see what breaks if the opposite decision is taken.

---

## Case 1 — High-volume inference

Company with ~2,000 requests per minute of a support model. TTFT needs to stay below 400 ms and the cost per million tokens cannot exceed a defined ceiling.

![Case Study: Inference at Scale](../assets/ch15-case-inference.svg)

| Aspect               | Decision                              | Reason                              | What would break if it were the opposite |
|----------------------|---------------------------------------|-------------------------------------|----------------------------------------------|
| Engine               | vLLM on AKS                           | Continuous batching + PagedAttention | Managed Foundry: higher cost per token at volume |
| Architecture         | Disaggregated (Prefill + Decode)      | Optimize TTFT and throughput        | Single pool: TTFT rises or utilization falls |
| Hardware             | ND-series H100                        | HBM + NVLink                        | SKU without enough HBM: OOM or aggressive quantization |
| Scale                | Autoscaling by utilization            | Cost control                        | Fixed scale: pays for idle or drops requests |
| Model                | FP8                                   | Fits in HBM                         | FP16: fewer requests per GPU             |

Order of magnitude: cluster of dozens of GPUs. Without disaggregated, TTFT worsened ~2x on long prompts. Operational complexity accepted in exchange for competitive cost per token.

---

## Case 2 — Controlled agent with RAG

Internal system to query policies and open tickets. Moderate volume, but risk of unwanted action and cost explosion.

![Case Study: Agent with RAG](../assets/ch15-case-agent.svg)

| Aspect               | Decision                              | Reason                              | What would break if it were the opposite |
|----------------------|---------------------------------------|-------------------------------------|----------------------------------------------|
| Pattern              | Agent + RAG                           | Needs data + actions                | Inference only: does not execute the action |
| Limit                | Maximum of 6 steps                    | Control amplification               | No limit: cost per execution 10–20x higher |
| Tools                | Limited and audited                   | Reduce side-effects                 | Broad tools: risk of unaudited action    |
| HITL                 | For actions that change data          | Security                            | No HITL: critical actions without approval |
| Observability        | Cost per execution + steps            | Visibility                          | Infra metrics only: does not know why cost rose |

Order of magnitude: without the step limit, one execution could cost the equivalent of 15–20 simple inference calls. The limit keeps predictability.

---

## Case 3 — Hybrid (Closed + Open)

Sensitive data cannot leave the perimeter. The rest of the volume is high and can use a closed model.

| Aspect               | Decision                              | Reason                              | What would break if it were the opposite |
|----------------------|---------------------------------------|-------------------------------------|----------------------------------------------|
| Router               | Data classification                   | Data residency                      | No router: either everything leaves or everything is expensive |
| Sensitive path       | Self-hosted open weights              | Data does not leave                 | Everything in Foundry: violates residency |
| General path         | Closed model                          | Quality                             | Everything self-hosted: high fixed operations and cost |
| Fallback             | Simple path always available          | Resilience                          | No fallback: router failure stops the system |

Order of magnitude: the self-hosted path is only justified above a stable volume that amortizes the GPUs. Below that the fixed cost does not compensate.

---

## Lessons

Start simple. Add complexity only when you measure the problem that the current pattern does not solve.

Explicit limits (steps, tools, cost) are more reliable than relying on model behavior.

Observability needs to cover the agent level, not just infrastructure. Without this FinOps arrives late.

The router is often the place where the most important decisions happen. Concentrate the logic there.

---

## Checklist

- [ ] Can I say what would break in each case if the opposite decision had been taken?
- [ ] Do I have an order of magnitude of cost or scale for each case?
- [ ] Do I know when disaggregated serving is worth the complexity?
- [ ] Can I propose clear limits for an agent before putting it in production?
- [ ] Do I recognize the role of the router in hybrid architectures?

---

Next: Decision Matrices and Checklists. Ready-to-use tools for meetings.
