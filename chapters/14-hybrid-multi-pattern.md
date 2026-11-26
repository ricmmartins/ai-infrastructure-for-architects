# Capítulo 14 — Hybrid e Multi-pattern Architectures

Arquiteturas reais raramente usam um único padrão. Elas combinam vários. O trabalho do arquiteto é definir onde cada padrão começa e termina, e o que acontece na fronteira.

---

## Por que híbridos aparecem

Sistemas de produção misturam modelos fechados e open weights, RAG e agents, serviços gerenciados e self-hosted. Cada combinação resolve um problema e cria outro. O erro comum é espalhar a lógica de decisão por todo o sistema em vez de concentrá-la.

---

## Padrões híbridos comuns

![Padrões Híbridos Comuns](../assets/ch14-hybrid-patterns.svg)

| Padrão Híbrido          | Quando usar                              | Benefício principal               | Quando não vale a pena                |
|------------------------|------------------------------------------|----------------------------------|----------------------------------------|
| Closed + Open          | Volume alto + qualidade em casos difíceis| Custo otimizado                  | Volume baixo ou equipe pequena         |
| RAG + Agent            | Tarefas que precisam de dados e ação    | Capacidade maior                 | Tarefa simples que inference resolve   |
| Managed + Self-hosted  | Transição ou controle seletivo          | Equilíbrio operação/controle     | Sem critério claro de roteamento       |
| Prefill/Decode split   | Clusters grandes de inference            | Utilization melhor               | Clusters pequenos                      |

A maioria dos híbridos só se justifica depois que você mediu o problema que o padrão simples não resolve.

---

## O router como fronteira

![Composição de Padrões](../assets/ch14-composition.svg)

Um router (ou camada de orquestração) é o lugar onde a maioria das decisões de padrão deve acontecer:

- Qual modelo usar?
- Precisa de RAG?
- Deve ir para um agent?
- Qual pool de inference?

Concentrar a lógica ali evita que cada serviço invente sua própria regra. Também facilita medir custo, latência e qualidade por caminho.

---

## Princípios que evitam complexidade desnecessária

Fronteiras claras. Cada padrão tem responsabilidade definida. Se não consegue explicar em uma frase o que aquele caminho faz, ele provavelmente está fazendo demais.

Observabilidade na fronteira. Meça custo, latência e qualidade em cada caminho. Sem isso você não sabe se o híbrido está ganhando ou só adicionando operação.

Fallback. Tenha um caminho simples quando o caminho complexo falhar. Agents e routers complexos falham. O sistema precisa continuar funcionando.

Evolução gradual. Comece simples. Adicione o próximo padrão só quando houver evidência de que o atual não resolve.

---

## Quando usar / quando não usar

| Situação                              | Abordagem                             | Motivo principal                  |
|--------------------------------------|---------------------------------------|-----------------------------------|
| Otimizar custo em volume alto        | Closed + Open com router              | Break-even mensurável             |
| Tarefas complexas com dados          | RAG + Agent                           | Capacidade                        |
| Equipe em transição                    | Managed + Self-hosted                 | Risco controlado                  |
| Sistema ainda pequeno                | Evite híbridos                        | Complexidade prematura            |
| Requisitos muito diferentes por request | Router bem definido                 | Decisão centralizada              |

---

## Checklist

- [ ] Consigo identificar quando um sistema realmente precisa de mais de um padrão?
- [ ] A lógica de roteamento está concentrada ou espalhada?
- [ ] Cada caminho tem observabilidade de custo e qualidade?
- [ ] Existe fallback simples?
- [ ] Consigo explicar por que o híbrido é necessário e não só interessante?

---

Com este capítulo fechamos a Part IV. Próximo: Case Studies. É onde os padrões se encontram com restrições reais.
