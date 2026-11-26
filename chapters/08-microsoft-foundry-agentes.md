# Chapter 8 — Microsoft Foundry and Agent Patterns

Foundry takes GPU operations off your plate. What remains is deciding what you cannot outsource: agent limits, data residency and when the cost of a tool loop justifies the risk.

---

## What the platform actually abstracts

Foundry gives access to models, agent orchestration and evaluation in a managed environment. This reduces the time to the first working agent. It does not reduce the responsibility for decisions that affect cost, risk and quality.

![Microsoft Foundry overview for Architects](../assets/ch8-foundry-overview.svg)

| Layer               | What the platform does                    | What remains your problem                  |
|---------------------|-------------------------------------------|--------------------------------------------|
| Model Catalog       | Makes closed and open models available    | Choosing the model and the routing point   |
| Agent Service       | Runs the loop, tools and memory           | Defining step limits, tools and HITL       |
| Evaluation          | Offers quality and safety metrics         | Deciding which metrics matter for the business |
| Infrastructure      | Provisions compute, network and identity  | Isolation, governance and data residency   |

In practice, most teams use Foundry to deliver the first agent and only leave it when the cost per execution or the requirement for inference control becomes larger than what the platform solves.

---

## Agent patterns and when each one breaks

![Agent Patterns in Foundry](../assets/ch8-agent-patterns.svg)

| Pattern             | Complexity | Control | When it works                          | When it breaks                             |
|---------------------|------------|---------|----------------------------------------|--------------------------------------------|
| Single Agent        | Low        | High    | Well-defined task, few tools           | Needs specialization or long context       |
| Agent + Tools       | Medium     | High    | Most business cases                    | Tools have side-effects without HITL       |
| Multi-Agent         | High       | Medium  | Process with clearly separated stages  | Coordination becomes more expensive than the value |

Start with Single Agent or Agent + Tools. Multi-Agent is only worth it when you have already measured that a single agent cannot maintain quality or that context cost is exploding.

---

## Foundry vs self-managed: the real trade-off

| Criterion                    | Foundry                          | AKS + vLLM/SGLang                     |
|------------------------------|----------------------------------|---------------------------------------|
| Time to first agent          | Days                             | Weeks                                 |
| Inference control            | Limited                          | Full (quantization, batching, cache)  |
| GPU operations               | Low                              | High                                  |
| Cost at high and stable volume| Can become more expensive        | Usually becomes lower                 |
| Data residency / isolation   | Depends on configuration         | Full                                  |

Foundry wins when the team is small or the volume does not yet justify operating GPUs. Self-managed wins when the cost per million tokens or the requirement that data cannot leave the perimeter becomes the main problem.

---

## Governance that matters

Three points that break agents in production:

1. Tools with side-effects without human approval.
2. Absence of a step limit (cost grows without a ceiling).
3. Lack of observability of the entire loop (not just the model).

Even in Foundry you need cost per execution, number of steps, tool failure rate and a simple fallback path.

---

## When to use / when not to use

| Situation                             | Recommendation                        | Main reason                          |
|--------------------------------------|---------------------------------------|--------------------------------------|
| First agent, small team              | Foundry                               | Delivery speed                       |
| High volume and cost per token matters| AKS + open source engine             | Utilization control                  |
| Sensitive data that cannot leave     | Self-managed or hybrid                | Data residency                       |
| Needs deep fine-tuning               | Evaluate self-managed                 | Foundry has customization limits     |
| Very specific network requirements   | Self-managed                          | Isolation control                    |

---

## Checklist

- [ ] Do I know what Foundry takes off my plate and what remains my problem?
- [ ] Do I have step limits and HITL defined before putting the agent in production?
- [ ] Can I justify Foundry vs self-managed with volume numbers or data requirements?
- [ ] Is cost per execution observable?
- [ ] Is there a simple fallback if the tool loop fails?

---

Next chapter: Compute Patterns on Azure. This is where platform decisions meet concrete SKU and orchestration choices.
