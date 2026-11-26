# Capítulo 10 — Networking, Storage e Data Patterns

Em AI, rede e storage deixam de ser commodity. Eles determinam se o training termina a tempo, se o RAG cabe no SLA de latência e se o custo de dados explode.

---

## Por que muda o jogo

Em workloads tradicionais você dimensiona rede e storage depois. Em AI eles entram no design desde o início porque:

- Training distribuído é limitado por bandwidth entre GPUs.
- RAG adiciona latência de retrieval que compete com o tempo de geração.
- Checkpoints grandes e frequentes viram custo e risco de retomada.

---

## Padrões de dados

![Padrões de Dados para AI](../assets/ch10-data-patterns.svg)

| Tipo de dado         | Característica                    | Serviço típico                  | O que quebra se errar              |
|----------------------|---------------------------------|---------------------------------|------------------------------------|
| Training data        | Alto volume, alto throughput    | Blob / ADLS                     | Job lento ou custo de egresso     |
| Checkpoints          | Escrita grande e frequente      | Blob                            | Perda de progresso ou custo alto  |
| Vector / RAG         | Baixa latência de busca           | Azure AI Search                 | Latência total acima do SLA        |
| Model artifacts      | Versionamento e rollback        | Blob + tags                     | Deploy que não reverte             |

Defina lifecycle de checkpoints desde o primeiro job. Sem isso o storage cresce sem controle.

---

## Rede por modo

![Considerações de Rede para AI](../assets/ch10-network-considerations.svg)

| Modo          | Requisito real                     | Decisão típica                     | Quando exagera                      |
|---------------|------------------------------------|-------------------------------------|-------------------------------------|
| Training      | Bandwidth alta + baixa latência    | InfiniBand                          | Clusters pequenos ou inference      |
| Inference     | Latência para o usuário            | Private Link + proximidade          | InfiniBand                          |
| RAG           | Latência para o vector store        | Co-localização quando possível       | Vector store em outra região         |
| Agents        | Chamadas a múltiplos sistemas        | Observabilidade e governança         | Rede aberta sem controle            |

InfiniBand para inference é o erro mais comum. O custo sobe e o ganho de latência é desprezível comparado com o tempo de decode.

---

## Decisões que importam

Onde colocar o vector store de RAG? Perto do modelo reduz latência. Em serviço gerenciado reduz operação. A escolha depende do SLA e do volume. Em volume baixo, gerenciado quase sempre ganha.

Para training, prefira leitura direta de Blob/ADLS. Copiar dados para o cluster antes do job adiciona tempo e custo sem benefício claro na maioria dos casos.

Private Endpoints para Storage, AI Search e Foundry são o padrão em ambientes enterprise. Exposição pública desnecessária cria risco sem ganho de performance.

---

## Quando usar / quando não usar

| Situação                              | Recomendação                          | Motivo principal                  |
|--------------------------------------|---------------------------------------|-----------------------------------|
| Training em larga escala             | InfiniBand + Blob de alto throughput  | Bandwidth entre nós               |
| RAG com SLA agressivo de latência    | Vector store co-localizado            | Reduzir round-trip                |
| Dados sensíveis                       | Private Endpoints                     | Isolamento                        |
| Volume baixo de RAG                  | Serviço gerenciado                    | Menos operação                    |
| Muitos checkpoints                   | Lifecycle agressiva                   | Controle de custo                 |

---

## Checklist

- [ ] Sei por que InfiniBand quase nunca vale para inference?
- [ ] Tenho lifecycle definida para checkpoints?
- [ ] Consigo explicar o impacto de latência do vector store no RAG?
- [ ] Private Endpoints são o padrão ou ainda estão como exceção?
- [ ] Consigo estimar se o storage vai ser um custo relevante?

---

Próximo capítulo: Landing Zones e Governança. A base que impede que cada time invente a sua própria forma de fazer a mesma coisa.
