# Capítulo 17 — Glossário Visual para Arquitetos

Se você consegue explicar estes conceitos com clareza, consegue conversar de igual para igual com clientes avançados.

---

## Mapa do Handbook

![Mapa de Conceitos do Handbook](../assets/ch17-glossary-overview.svg)

---

## Glossário

| Termo                    | Definição para o arquiteto                                      | Capítulo |
|--------------------------|----------------------------------------------------------------|---------|
| Prefill                  | Fase que processa o prompt inteiro (compute-bound)             | 4       |
| Decode                   | Fase que gera tokens um a um (memory-bound)                    | 4       |
| Continuous Batching      | Adiciona e remove requests dinamicamente para melhor utilization | 4     |
| PagedAttention           | Gerencia KV cache como páginas de memória                      | 4       |
| Disaggregated Serving    | Separa pools de Prefill e Decode                               | 4       |
| HBM                      | High Bandwidth Memory — recurso mais escasso em inference     | 3       |
| NVLink                   | Interconexão de alta banda dentro do nó (scale-up)            | 3       |
| InfiniBand               | Rede de baixa latência entre nós (scale-out)                  | 3       |
| KV Cache                 | Memória que armazena o estado do contexto durante a geração  | 3, 4    |
| RAG                      | Retrieval-Augmented Generation                                 | 5       |
| Agent                    | Sistema que executa um loop de perception-planning-action      | 6       |
| Open Weights             | Modelos cujos pesos são publicamente disponíveis               | 7       |
| Quantização             | Redução da precisão dos pesos para economizar memória          | 7       |
| TTFT                     | Time to First Token                                            | 4       |
| HITL                     | Human-in-the-Loop                                              | 6, 13   |
| Landing Zone             | Estrutura base de governança e plataforma no Azure            | 11      |

---

## Perguntas que o arquiteto deve saber responder

1. Qual a diferença entre Prefill e Decode e por que isso importa?
2. Quando faz sentido usar disaggregated serving?
3. Por que HBM é o recurso mais crítico em inference?
4. Quando um agent se justifica e como controlar seu custo?
5. Em que situações open weights são a escolha correta?
6. Como desenhar uma arquitetura híbrida sem criar complexidade desnecessária?
7. Quais são os principais drivers de custo de um sistema de AI?
8. Como tratar data residency em uma arquitetura de AI?

---

## Encerramento

Este handbook foi construído para arquitetos que precisam entender como sistemas de AI realmente funcionam e tomar decisões conscientes, sem precisar se tornar desenvolvedores ou operadores.

Os capítulos anteriores deram profundidade. Este glossário serve como referência rápida.

O objetivo foi atingido se você conseguir:
- Conversar no mesmo nível de clientes avançados
- Justificar decisões de arquitetura com trade-offs claros
- Evitar complexidade desnecessária
- Desenhar sistemas que equilibram capacidade, custo e risco

Para a parte operacional (deployment, troubleshooting, GPU, IaC, monitoramento), veja o projeto irmão [AI for Infra Pros](https://www.ai4infra.com/).

---

Fim do Handbook — AI for Architects

Repositório: https://github.com/ricmmartins/ai-infrastructure-for-architects
