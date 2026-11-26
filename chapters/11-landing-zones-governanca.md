# Chapter 11 — Landing Zones and Governance for AI

A good Landing Zone does not prevent innovation. It prevents each team from inventing its own way of doing the same thing and creating debt or risk that the rest of the company pays for later.

---

## Why AI needs specific governance

AI workloads add three problems that traditional landing zones did not cover well:

- GPU cost that grows fast and is hard to predict.
- Agents that execute actions with side-effects.
- Data that cannot leave a defined perimeter.

Without guardrails, the first team that puts an agent in production defines the standard (or the lack of it) for the next ones.

---

## Structure

![Landing Zone for AI](../assets/ch11-landing-zone.svg)

| Layer               | Responsibility                            | What happens if it fails              |
|---------------------|-------------------------------------------|---------------------------------------|
| Management          | Global policies                           | Expensive SKUs released without control |
| Platform            | Identity, network, monitoring             | Each workload invents its own         |
| Shared Services     | AI Search, Storage, Key Vault             | Duplication and inconsistency         |
| Workloads           | Inference, training, agents               | Weak isolation or invisible cost      |

The separation between platform and workloads is what allows the AI team to move without asking permission for every resource, as long as it stays within the guardrails.

---

## Layers that matter

![Governance Layers for AI](../assets/ch11-governance-layers.svg)

Policies: restrict regions and GPU SKUs, require cost tags. Without this the first H100 cluster appears in a test subscription.

Identity: Managed Identity and least privilege for agents and tools. Agents with broad permissions are the newest risk vector.

Network: Private Endpoints as standard. Public exposure of AI Search or Storage creates risk without measurable gain in most cases.

Observability and cost: every AI workload needs visibility of cost per execution or per job. Agents need traceability of steps and tool calls. Without this FinOps arrives late.

---

## Practical decisions

| Decision                             | Recommendation                            | Why                                    |
|--------------------------------------|-------------------------------------------|----------------------------------------|
| How many subscriptions?              | Separate by environment and criticality   | Isolation of cost and risk             |
| Who creates GPU resources?           | Policy + RBAC                             | Avoid cost surprise                    |
| Sensitive data                       | Dedicated subscription or resource group  | Clear perimeter                        |
| Network standard                     | Hub-spoke or Virtual WAN + Private Link   | Consistency                            |
| Observability                        | Centralized from the start                | Cost and risk visible early            |

---

## When to use / when not to use

| Situation                             | Approach                              | Main reason                       |
|--------------------------------------|---------------------------------------|-----------------------------------|
| Multiple AI teams                    | Structured Landing Zone               | Avoid divergence                  |
| Small pilot project                  | Can start simpler                     | Speed                             |
| Strong compliance requirements       | Rigid governance from the start       | Regulatory risk                   |
| Expected rapid growth                | Invest in the Landing Zone early      | Cost of fixing later              |

---

## Checklist

- [ ] Can I explain to a stakeholder what the Landing Zone prevents from happening?
- [ ] Do I know which GPU policies and tags are the minimum viable?
- [ ] Do agents and tools have least privilege defined?
- [ ] Are Private Endpoints the standard or still an exception?
- [ ] Is cost per AI workload visible before the first month of production?

---

With this chapter we close Part III. Next: Cost, FinOps and Observability. This is where architecture decisions meet the number the business actually sees.
