# Chapter 6 — Agents and Agentic Systems
## Perception, Planning, Action and cost amplification

> “An agent is not smarter inference. It is a system that can multiply in cost and risk at every step.”

---

## What an agent is for the architect

An agent is a system that:
1. **Perceives** the current state
2. **Plans** the next step
3. **Acts** (calling a model, a tool or another system)
4. Repeats until it reaches the goal or a limit

Unlike simple inference, the agent maintains state, makes intermediate decisions and can cause side-effects.

---

## The fundamental loop

![Basic Agent loop](../assets/ch6-agent-loop.svg)

Each turn of the loop consumes:
- Tokens (cost)
- Time (latency)
- Risk of failure or unexpected behavior

---

## Cost and latency amplification

![Cost Amplification in Agents](../assets/ch6-cost-amplification.svg)

| System type              | Model calls | Relative cost | Relative latency |
|--------------------------|-------------|---------------|------------------|
| Simple inference         | 1           | 1×            | 1×               |
| Agent with 5 steps       | 5–8         | 8–12×         | 5–10×            |
| Agent with tools         | 8–15        | 12–20×        | 8–15×            |
| Multi-agent              | 15–40       | 20–50×        | 15–30×           |

**Architect's rule:** define a maximum number of steps and have cost observability per execution.

---

## Types of memory

| Type              | Duration    | Typical use                    | Architectural impact          |
|-------------------|-------------|--------------------------------|-------------------------------|
| Short-term        | Session     | Conversation context           | KV cache / context window     |
| Long-term         | Persistent  | Preferences, history           | Vector DB or database         |
| Episodic          | Per task    | Previous agent steps           | Orchestration state           |

---

## Tools and side-effects

Tools are how the agent interacts with the real world (APIs, databases, internal systems).

**Architect decisions:**
- Which tools can the agent call?
- Are they idempotent?
- Is there human approval for critical actions?
- How to isolate the blast radius of a misbehaving tool?

---

## Multi-agent

Using several specialized agents can improve quality, but dramatically increases complexity and cost.

**When to consider:**
- Tasks that require very different expertise
- Need for debate or cross-verification
- Systems where separation of responsibilities reduces risk

**When to avoid:**
- Most cases at the beginning
- When cost and latency are critical

---

## Decision framework

| Question                              | If yes →                          | If no →                      |
|---------------------------------------|-----------------------------------|-------------------------------|
| Does the task require multiple steps? | Consider agent                    | Use simple inference          |
| Are there side-effects?               | Add controls and HITL             | Can be more autonomous        |
| Does cost per execution need to be predictable? | Limit steps + observability | Monitor and adjust later      |
| Need long-term memory?                | Add external store                | Session context is enough     |

---

## When to use / when not to use

| Situation                             | Recommendation                        |
|--------------------------------------|---------------------------------------|
| Single-step task                     | Simple inference                      |
| Process with 3–7 well-defined steps | Agent with step limit                 |
| Actions that change external systems | Agent + human-in-the-loop             |
| Need for maximum autonomy            | Multi-agent (with care)               |
| Tight cost budget                    | Avoid agents or limit strongly        |

---

## Chapter checklist

- [ ] Can I explain the difference between inference and agent to a stakeholder?
- [ ] Do I understand the cost amplification effect?
- [ ] Do I know when to put human-in-the-loop?
- [ ] Do I have a framework to decide if I need multi-agent?
- [ ] Can I map an agent to Microsoft Foundry or custom orchestration?

---

## What comes next

In Chapter 7 we talk about **Open Weights and Local AI**. When self-hosting makes sense, what the real trade-offs are and how to design hybrid architectures.
