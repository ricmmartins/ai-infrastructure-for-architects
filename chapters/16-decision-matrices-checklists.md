# Chapter 16 — Decision Matrices and Checklists
## Ready-to-use tools for the architect's daily work

> “The value of this handbook is in helping you make better decisions, faster.”

---

## How to use this chapter

The matrices and checklists below consolidate the content of the previous chapters. Use them as a starting point in design meetings, architecture reviews or when you need to justify a decision.

---

## Mode Matrix

![Mode Decision Matrix](../assets/ch16-mode-decision.svg)

| Need                                 | Recommended mode     | Complexity | Relative cost |
|--------------------------------------|----------------------|------------|---------------|
| Single response, no state            | Simple inference     | Low        | 1×            |
| Needs updated external data          | RAG                  | Medium     | 1.5–3×       |
| Multiple steps or actions            | Agent (with limits)  | High       | 5–20×         |
| Combination of data + action         | Agent + RAG          | High       | 8–25×         |

---

## Compute Matrix

| Scenario                             | Recommended pattern         | Note                           |
|--------------------------------------|----------------------------|--------------------------------|
| Start fast                           | Foundry / Managed          | Less operations                |
| Inference at scale with control      | AKS + vLLM/SGLang          | Control/operations balance     |
| Large-scale training                 | CycleCloud / Slurm         | Long jobs                      |
| High volume + sensitive data         | Hybrid (Managed + Self)    | Decision router                |

---

## Open Weights Matrix

| Factor                       | Favors Open Weights | Favors Closed API |
|------------------------------|---------------------|-------------------|
| Data residency               | Yes                 | No                |
| Very high volume             | Yes                 | No                |
| Time-to-market               | No                  | Yes               |
| Need for fine-tuning         | Yes                 | No                |
| Small team                   | No                  | Yes               |

---

## Inference Design Checklist

- [ ] Does the model fit in the available HBM (with quantization)?
- [ ] Are Prefill and Decode optimized for the load profile?
- [ ] Is continuous batching enabled?
- [ ] Is there an autoscaling strategy?
- [ ] Are TTFT and throughput being measured?
- [ ] Is cost per million tokens within expectations?

---

## Agent Design Checklist

- [ ] Is there a maximum step limit?
- [ ] Are tools at the least privilege possible?
- [ ] Do critical actions have human-in-the-loop?
- [ ] Is cost per execution observable?
- [ ] Is there a fallback to a simple path?
- [ ] Are logs of steps and tool calls being collected?

---

## Hybrid Architecture Checklist

- [ ] Does the router have clear decision criteria?
- [ ] Does each path have its own observability?
- [ ] Is there a fallback?
- [ ] Is the additional complexity justified?
- [ ] Can the team operate the solution?

---

## Security and Compliance Checklist

- [ ] Do sensitive data have a defined path (self-hosted or isolated)?
- [ ] Are Private Endpoints being used where it makes sense?
- [ ] Do agents have action auditing?
- [ ] Do Azure Policy policies cover GPU and regions?
- [ ] Does data classification influence routing?

---

## FinOps Checklist

- [ ] Are AI workloads tagged?
- [ ] Are there cost alerts?
- [ ] Is GPU utilization being monitored?
- [ ] Do agents have a cost or step limit?
- [ ] Is there periodic review of models (cost vs quality)?

---

## What comes next

In Chapter 17 (final) we have the **Visual Glossary for Architects**, consolidating the main concepts and diagrams of the handbook.
