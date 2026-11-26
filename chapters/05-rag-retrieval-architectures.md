# Capítulo 5 — RAG e Retrieval Architectures

RAG não é um banco de vetores com um LLM na frente. É a decisão de onde colocar a verdade e quanto de latência você aceita para isso.

---

## Por que existe

Modelos têm conhecimento congelado no treinamento. RAG traz informação externa no momento da geração. Para o arquiteto isso é um trade-off de latência, frescor, custo e precisão.

---

## Fluxo

![Fluxo básico de RAG](../assets/ch5-rag-flow.svg)

Query chega, retrieval busca documentos, documentos entram no contexto, modelo gera. O retrieval está no caminho de latência. Cada milissegundo na busca é milissegundo que o usuário espera.

---

## Padrões

![Padrões de RAG](../assets/ch5-rag-patterns.svg)

| Padrão              | Complexidade | Latência   | Precisão     | Quando usar                          | Quando não usar                     |
|--------------------|--------------|-----------|-------------|--------------------------------------|-------------------------------------|
| RAG Simples        | Baixa        | Baixa     | Média       | Conhecimento estático, FAQs          | Queries que precisam de precisão alta |
| Hybrid Search      | Média        | Média     | Alta        | Maioria dos casos de produção        | Volume baixo onde simples basta     |
| Agentic RAG        | Alta         | Alta      | Muito alta  | Queries complexas, multi-hop         | SLA agressivo de latência            |
| Graph RAG          | Alta         | Média-Alta| Alta        | Dados com relacionamentos fortes     | Dados sem estrutura de grafo        |

---

## Decisões

Onde vive o índice? Managed (Azure AI Search) reduz operação e cobra por query. Self-hosted dá controle e adiciona operação. Em volume baixo, managed quase sempre ganha.

Estratégia de retrieval: vector puro é rápido e perde matches exatos. Hybrid melhora precisão na maioria dos casos. Reranking aumenta precisão e adiciona 100–300 ms.

Frescor: dados quase estáticos aceitam reindexação periódica. Dados que mudam com frequência precisam de indexação incremental. Dados em tempo real raramente cabem em RAG tradicional sem cache curto.

Cada retrieval adiciona tipicamente 50–200 ms. Agentic RAG pode multiplicar o número de retrievals e a latência.

---

## Quando usar / quando não usar

| Situação                              | Recomendação                          | O que quebra se errar                     |
|--------------------------------------|---------------------------------------|-------------------------------------------|
| Conhecimento interno da empresa      | RAG Hybrid                            | Só fine-tuning: dados ficam desatualizados |
| Dados que mudam constantemente       | RAG com indexação incremental         | Fine-tuning: lento demais                 |
| Necessidade de citação de fontes      | RAG                                   | Sem RAG: modelo inventa fonte             |
| Tarefa puramente generativa          | Sem RAG                               | RAG: latência desnecessária               |
| Volume muito alto + dados estáticos  | Avaliar fine-tuning ou cache          | RAG puro: custo por query sobe            |

---

## Checklist

- [ ] Consigo explicar por que RAG coloca latência no caminho crítico?
- [ ] Sei a diferença entre RAG simples, hybrid e agentic?
- [ ] Tenho critério para decidir entre managed e self-hosted?
- [ ] Entendo o trade-off entre frescor dos dados e complexidade de indexação?
- [ ] Consigo mapear uma arquitetura RAG para Azure?

---

Próximo: Agents e Sistemas Agentic. Onde o sistema deixa de só responder e passa a agir.
