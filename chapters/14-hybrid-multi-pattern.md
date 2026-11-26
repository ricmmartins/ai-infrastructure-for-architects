# Capítulo 14 — Hybrid e Multi-pattern Architectures
## Como combinar padrões em sistemas reais

> “Arquiteturas reais raramente usam um único padrão. Elas combinam vários, e o arquiteto precisa saber onde colocar as fronteiras.”

---

## Por que padrões híbridos

Até aqui vimos padrões de forma isolada. Na prática, sistemas de produção misturam:

- Modelos fechados e open weights
- RAG e agents
- Serviços gerenciados e self-hosted
- Diferentes estratégias de inference

O trabalho do arquiteto é definir **onde** cada padrão é usado e **como** eles se comunicam.

---

## Padrões híbridos comuns

![Padrões Híbridos Comuns](../assets/ch14-hybrid-patterns.svg)

| Padrão Híbrido          | Quando usar                              | Principal benefício               |
|------------------------|------------------------------------------|----------------------------------|
| **Closed + Open**          | Volume alto + necessidade de qualidade   | Otimização de custo             |
| **RAG + Agent**            | Tarefas que precisam de dados + ação    | Maior capacidade                 |
| **Managed + Self-hosted**  | Transição gradual ou controle seletivo  | Equilíbrio operação/controle    |
| **Prefill/Decode split**   | Clusters grandes de inference            | Melhor utilization               |

---

## Composição de padrões

![Composição de Padrões](../assets/ch14-composition.svg)

Um **router** (ou camada de orquestração) é frequentemente o ponto onde as decisões de padrão acontecem:

- Qual modelo usar?
- Precisa de RAG?
- Deve ir para um agent?
- Qual pool de inference?

Concentrar essa lógica evita espalhar complexidade por todo o sistema.

---

## Princípios para arquiteturas híbridas

1. **Fronteiras claras**  
   Cada padrão deve ter responsabilidade bem definida.

2. **Observabilidade na fronteira**  
   Meça custo, latência e qualidade em cada caminho.

3. **Fallback**  
   Tenha um caminho simples quando o caminho complexo falhar.

4. **Evolução gradual**  
   Comece simples e adicione padrões conforme a necessidade for comprovada.

---

## Quando usar / quando não usar

| Situação                              | Abordagem                             |
|--------------------------------------|---------------------------------------|
| Necessidade de otimizar custo        | Closed + Open com router              |
| Tarefas complexas com dados          | RAG + Agent                           |
| Equipe em transição                    | Managed + Self-hosted                 |
| Sistema ainda pequeno                | Evite híbridos desnecessários           |
| Requisitos muito diferentes por request | Router bem definido                 |

---

## Checklist do capítulo

- [ ] Consigo identificar quando um sistema precisa de mais de um padrão?
- [ ] Sei onde colocar a lógica de roteamento?
- [ ] Entendo os principais padrões híbridos e seus trade-offs?
- [ ] Tenho princípios para evitar complexidade desnecessária?
- [ ] Consigo desenhar uma arquitetura que evolui de simples para híbrida?

---

## O que vem a seguir

Com o Capítulo 14 fechamos a **Part IV — Decision Frameworks**.

No Capítulo 15 entramos na **Part V — Practice**, começando pelos **Case Studies Profundos**.

Quer ajustes neste capítulo ou seguimos para o Capítulo 15?
