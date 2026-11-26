# Capítulo 12 — Cost, FinOps e Observabilidade

Em AI o custo não é consequência. É variável de design. Decisões de modelo, contexto, steps e utilization mudam o custo em ordens de magnitude.

---

## Drivers principais

![Principais Drivers de Custo em AI](../assets/ch12-cost-drivers.svg)

| Driver              | O que influencia                          | Alavanca do arquiteto                  |
|---------------------|-------------------------------------------|----------------------------------------|
| Compute (GPU)       | Utilization, tipo de GPU, tempo de uso    | Batching, quantização, right-sizing    |
| Tokens              | Tamanho de input/output, steps de agent   | Limitar contexto e steps               |
| Dados & Storage     | Volume de training data, vector DB        | Lifecycle, retenção                    |
| Rede                | Egress, transferência de dados             | Co-localização, Private Link            |

---

## Modelo de custo por modo

| Modo          | Unidade principal de custo     | Como controlar                           |
|---------------|--------------------------------|------------------------------------------|
| Training      | GPU-hours                      | Duração do job, eficiência de paralelismo |
| Inference     | Custo por milhão de tokens      | Modelo, quantização, batching           |
| Agents        | Tokens × steps                  | Limite de steps, escolha de tools       |
| RAG           | Retrieval + tokens             | Qualidade do índice, reranking         |

---

## Observabilidade

![Camadas de Observabilidade para AI](../assets/ch12-observability-layers.svg)

Três níveis:

1. Infraestrutura — GPU utilization, latência, erros.
2. Modelo — tokens por request, TTFT, qualidade.
3. Agent / Aplicação — número de steps, tool calls, custo por execução.

Sem o terceiro nível você não sabe por que o custo de um agent explodiu.

---

## Práticas de FinOps

Tagging consistente dá visibilidade por time ou produto. Orçamentos e alertas evitam surpresa. Right-sizing de GPU reduz desperdício. Limite de steps em agents controla amplificação. Avaliação periódica de modelos permite trocar para opção mais barata quando a qualidade aceita.

---

## Quando usar / quando não usar

| Situação                              | Abordagem                             | O que quebra se não fizer               |
|--------------------------------------|---------------------------------------|---------------------------------------|
| Alto volume de inference             | Otimizar utilization e quantização     | Paga GPU ociosa ou modelo maior que o necessário |
| Agents em produção                    | Limites rígidos de steps + observabilidade | Custo por execução sem teto            |
| Training frequente                   | Monitorar GPU-hours de perto          | Jobs longos sem visibilidade          |
| Projeto piloto                       | Aceitar custo maior no início          | Otimizar cedo demais atrasa entrega   |
| Necessidade de previsibilidade       | Modelos gerenciados + limites         | Self-managed sem capacidade: surpresa |

---

## Checklist

- [ ] Consigo identificar os principais drivers de custo de um sistema de AI?
- [ ] Sei a diferença de modelo de custo entre training, inference e agents?
- [ ] Entendo por que observabilidade de agent é diferente de observabilidade tradicional?
- [ ] Tenho práticas de FinOps aplicáveis a workloads de AI?
- [ ] Consigo estimar o impacto de uma decisão arquitetural no custo?

---

Próximo: Security, Privacy e Compliance. Onde dados sensíveis e agents com side-effects mudam o design.
