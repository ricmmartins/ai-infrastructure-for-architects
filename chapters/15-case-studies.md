# Capítulo 15 — Case Studies

Os padrões só fazem sentido quando você vê o que quebra se a decisão oposta for tomada.

---

## Case 1 — Inference de alto volume

Empresa com ~2.000 requests por minuto de um modelo de suporte. TTFT precisa ficar abaixo de 400 ms e o custo por milhão de tokens não pode passar de um teto definido.

![Case Study: Inference em Escala](../assets/ch15-case-inference.svg)

| Aspecto              | Decisão                              | Motivo                              | O que quebraria se fosse o contrário          |
|----------------------|---------------------------------------|-------------------------------------|----------------------------------------------|
| Engine               | vLLM no AKS                           | Continuous batching + PagedAttention | Foundry gerenciado: custo por token mais alto em volume |
| Arquitetura          | Disaggregated (Prefill + Decode)      | Otimizar TTFT e throughput          | Pool único: TTFT sobe ou utilization cai     |
| Hardware             | ND-series H100                        | HBM + NVLink                        | SKU sem HBM suficiente: OOM ou quantização agressiva |
| Escala               | Autoscaling por utilization           | Controle de custo                   | Escala fixa: paga ocioso ou perde requests  |
| Modelo               | FP8                                   | Cabe na HBM                         | FP16: menos requests por GPU                |

Ordem de magnitude: cluster de dezenas de GPUs. Sem disaggregated, o TTFT piorava ~2x em prompts longos. Complexidade operacional aceita em troca de custo por token competitivo.

---

## Case 2 — Agent com RAG controlado

Sistema interno para consultar políticas e abrir tickets. Volume moderado, mas risco de ação indesejada e de custo explodir.

![Case Study: Agent com RAG](../assets/ch15-case-agent.svg)

| Aspecto              | Decisão                              | Motivo                              | O que quebraria se fosse o contrário          |
|----------------------|---------------------------------------|-------------------------------------|----------------------------------------------|
| Padrão               | Agent + RAG                           | Precisa de dados + ações             | Só inference: não executa a ação          |
| Limite               | Máximo de 6 steps                    | Controlar amplificação               | Sem limite: custo por execução 10–20x maior |
| Tools                | Limitadas e auditadas                 | Reduzir side-effects                | Tools amplas: risco de ação não auditada   |
| HITL                 | Para ações que alteram dados         | Segurança                            | Sem HITL: ações críticas sem aprovação     |
| Observabilidade      | Custo por execução + steps            | Visibilidade                        | Só métricas de infra: não sabe por que o custo subiu |

Ordem de magnitude: sem o limite de steps, uma execução podia custar o equivalente a 15–20 chamadas de inference simples. O limite mantém previsibilidade.

---

## Case 3 — Híbrido (Closed + Open)

Dados sensíveis não podem sair do perímetro. O restante do volume é alto e pode usar modelo fechado.

| Aspecto              | Decisão                              | Motivo                              | O que quebraria se fosse o contrário          |
|----------------------|---------------------------------------|-------------------------------------|----------------------------------------------|
| Router               | Classificação de dados                | Data residency                      | Sem router: ou tudo sai ou tudo fica caro   |
| Caminho sensível     | Open weights self-hosted              | Dados não saem                     | Tudo em Foundry: viola residency            |
| Caminho geral        | Modelo fechado                        | Qualidade                           | Tudo self-hosted: operação e custo fixo altos |
| Fallback             | Caminho simples sempre disponível      | Resiliência                         | Sem fallback: falha no router para o sistema |

Ordem de magnitude: o caminho self-hosted só se justifica acima de um volume estável que amortiza as GPUs. Abaixo disso o custo fixo não compensa.

---

## Lições

Comece simples. Adicione complexidade só quando medir o problema que o padrão atual não resolve.

Limites explícitos (steps, tools, custo) são mais confiáveis do que confiar no comportamento do modelo.

Observabilidade precisa cobrir o nível de agent, não só de infraestrutura. Sem isso o FinOps chega tarde.

O router é frequentemente o lugar onde as decisões mais importantes acontecem. Concentre a lógica lá.

---

## Checklist

- [ ] Consigo dizer o que quebraria em cada case se a decisão oposta tivesse sido tomada?
- [ ] Tenho uma ordem de magnitude de custo ou escala para cada case?
- [ ] Sei quando disaggregated serving vale a complexidade?
- [ ] Consigo propor limites claros para um agent antes de colocá-lo em produção?
- [ ] Reconheço o papel do router em arquiteturas híbridas?

---

Próximo: Decision Matrices e Checklists. Ferramentas prontas para usar em reunião.
