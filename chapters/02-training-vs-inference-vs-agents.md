# Capítulo 2 — Training vs Inference vs Agents

Se você desenhar a arquitetura pensando só em inference, o training vai doer. Se desenhar só para training, o agent vai te falir.

---

## O erro mais comum

Tratar AI como “uma aplicação que usa GPU”. Isso gera arquiteturas que funcionam razoavelmente para um modo e quebram nos outros dois.

---

## Trade-offs de sistema

![Trade-offs de Sistema entre Training, Inference e Agents](../assets/ch2-system-tradeoffs.svg)

| Dimensão          | Training                          | Inference                              | Agents                                   |
|-------------------|-----------------------------------|----------------------------------------|------------------------------------------|
| Padrão de carga   | Batch, longo, previsível            | Online, bursty, latência-sensível        | Multi-step, variável, side-effects         |
| Compute           | Alto utilization, collective ops  | Prefill (compute-bound) + Decode (memory-bound) | Múltiplas chamadas de inference           |
| Networking        | InfiniBand / NVLink críticos        | Baixa latência, alto throughput         | Chamadas a tools + APIs externas         |
| Estado            | Checkpoints + optimizer state     | KV cache (sessão)                       | Memória de longo prazo + contexto        |
| Tolerância a falha | Checkpoint e resume obrigatórios   | Retry e fallback de modelo             | Orquestração + human-in-the-loop        |
| Modelo de custo   | GPU-hours                         | Custo por milhão de tokens               | Tokens × número de steps                  |
| Escala            | Scale-up + scale-out              | Principalmente scale-out               | Orquestração e isolamento                |

---

## Training

Job de batch em larga escala. O objetivo é maximizar utilization durante horas ou semanas.

O arquiteto decide: número de GPUs por nó, interconexão (NVLink vs InfiniBand), estratégia de paralelismo e frequência de checkpoints. Rede de alta banda deixa de ser opcional. Storage precisa aguentar escrita massiva. O custo dominante é GPU-hour.

---

## Inference

Serviço online. Equilibrar TTFT e throughput.

Prefill é compute-bound. Decode é memory-bound. Continuous batching aumenta throughput e pode aumentar latência. Disaggregated serving melhora os dois e adiciona complexidade de rede. O arquiteto decide engine, quantização e se a complexidade de disaggregated vale o ganho.

---

## Agents

Não é “inference mais inteligente”. É um loop que faz múltiplas chamadas, chama tools e mantém estado.

Uma chamada de inference custa 1×. Um agent com 8 steps e 3 tools facilmente custa 10–20× em tokens e latência. O arquiteto decide onde orquestrar, quanto de memória de longo prazo é necessário, quando colocar human-in-the-loop e como isolar tools.

---

## Framework de decisão

![Framework de Decisão: Qual modo priorizar?](../assets/ch2-decision-framework.svg)

1. O sistema precisa criar modelos novos ou só servir modelos existentes?
2. A latência de resposta é crítica ou o throughput é mais importante?
3. Existem side-effects?
4. Qual é o orçamento de custo por interação aceitável?
5. Os dados podem sair do perímetro?

---

## Quando usar / quando não usar

| Necessidade                          | Foco principal     | O que quebra se misturar errado          |
|--------------------------------------|--------------------|------------------------------------------|
| Treinar ou fine-tune modelo grande   | Training           | Mesmo cluster de inference: jobs longos competem com SLA |
| API de chat com SLA de latência       | Inference          | Agent sem controle de steps: custo e latência explodem |
| Processo multi-step com tools        | Agents             | Sem observabilidade de steps: não sabe por que o custo subiu |
| Mistura de treinamento e serviço      | Separar clusters   | Um cluster “faz tudo”: nenhum dos dois fica bem |
| Volume baixo, rápido time-to-market  | Inference gerenciada | Self-hosted complexo no início: operação sem retorno |

---

## Checklist

- [ ] Consigo explicar a diferença de networking entre training e inference?
- [ ] Entendo por que disaggregated serving existe?
- [ ] Sei estimar a amplificação de custo de um agent?
- [ ] Tenho um framework para decidir se o sistema deve ser training-first, inference-first ou agent-first?
- [ ] Consigo mapear esses trade-offs para decisões de Azure?

---

Próximo: Compute, memória e conectividade. Onde os trade-offs de sistema encontram HBM, NVLink e InfiniBand.
