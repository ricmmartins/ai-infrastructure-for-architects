# Capítulo 9 — Compute Patterns no Azure
## ND-series, AKS, Foundry e CycleCloud

> “A escolha do padrão de compute define o quanto de controle e o quanto de operação você vai ter nos próximos 12 meses.”

---

## Os três padrões principais

![Padrões de Compute no Azure para AI](../assets/ch9-compute-patterns.svg)

| Padrão                  | Melhor para                     | Nível de controle | Operação | Exemplos de uso              |
|------------------------|---------------------------------|------------------|-----------|------------------------------|
| **Gerenciado**             | Velocidade e simplicidade      | Baixo            | Baixa     | Foundry, Azure OpenAI        |
| **AKS + Engine**           | Equilíbrio controle/operação   | Alto             | Média     | vLLM, SGLang em ND-series    |
| **HPC (CycleCloud/Slurm)** | Training em larga escala       | Muito alto       | Alta      | Fine-tuning e pre-training   |

---

## ND-series: o hardware

As VMs ND-series (H100, H200, B200) são a base da maioria dos workloads de AI no Azure.

**O que o arquiteto precisa saber:**
- Quantidade de GPUs por VM
- Presença de NVLink (scale-up)
- Opções de InfiniBand (scale-out)
- Quantidade de HBM

Essas características definem se o modelo cabe, se precisa de tensor parallel e qual o teto de escala.

---

## AKS + vLLM / SGLang

Este é o padrão mais comum quando o arquiteto quer controle sem construir tudo do zero.

**Vantagens:**
- Uso de engines otimizadas (PagedAttention, continuous batching)
- Integração com o ecossistema Kubernetes
- Possibilidade de misturar com outros workloads

**Decisões:**
- Tamanho do node pool
- Estratégia de autoscaling
- Como fazer rollout de novos modelos
- Observabilidade de GPU

---

## CycleCloud / Slurm

Usado principalmente para training e jobs longos.

**Quando faz sentido:**
- Jobs que duram horas ou dias
- Necessidade de scheduling avançado
- Clusters grandes com InfiniBand

**Trade-off:** maior complexidade operacional. Não é a melhor escolha para inference online.

---

## Framework de decisão

![Decisão de Compute Pattern](../assets/ch9-decision-compute.svg)

### Perguntas-chave
1. O workload é principalmente inference/agents ou training?
2. Quanto controle sobre o engine de inference você precisa?
3. Qual é a capacidade da equipe de operar Kubernetes ou Slurm?
4. O volume justifica a complexidade de self-managed?

---

## Quando usar / quando não usar

| Situação                              | Padrão recomendado                    |
|--------------------------------------|---------------------------------------|
| Começar rápido com agents             | Foundry                               |
| Inference em escala com controle     | AKS + vLLM/SGLang                     |
| Training de modelo grande            | CycleCloud / Slurm                    |
| Mistura de inference e training      | Separar os ambientes                  |
| Equipe sem experiência em GPU          | Comece gerenciado                     |

---

## Checklist do capítulo

- [ ] Consigo explicar a diferença entre os três padrões de compute?
- [ ] Sei quando usar AKS + vLLM vs Foundry?
- [ ] Entendo o papel do ND-series e InfiniBand?
- [ ] Tenho critérios claros para escolher o padrão?
- [ ] Consigo justificar a escolha para um stakeholder?

---

## O que vem a seguir

No Capítulo 10 vamos falar de **Networking, Storage e Data Patterns** para AI no Azure. Como a rede e o armazenamento afetam training, inference e RAG.

Quer ajustes neste capítulo ou seguimos para o Capítulo 10?
