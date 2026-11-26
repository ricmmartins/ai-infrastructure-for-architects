# Capítulo 1 — Por que AI é diferente
## Mental Models para Arquitetos

O melhor sistema de AI que eu já vi não foi desenhado por um cientista de dados. Foi desenhado por um arquiteto que entendeu que AI não é uma aplicação comum.

Este handbook é o projeto irmão do [AI for Infra Pros](https://www.ai4infra.com/). Enquanto aquele material foca em operação, deployment e troubleshooting, este foca em decisões de arquitetura.

---

## A reunião que muda o jogo

Você está numa call com um cliente digital native avançado. Eles falam de continuous batching, PagedAttention, disaggregated serving, agent loops e open weights como se fossem termos do dia a dia. Do outro lado da mesa, o time de infraestrutura tradicional ainda pensa em “subir uma API e colocar um load balancer na frente”.

Essa desconexão não é falta de conhecimento técnico. É falta de mental model.

Este capítulo constrói o modelo mental que um arquiteto precisa para conversar no mesmo nível desses clientes, sem precisar virar desenvolvedor de ML.

---

## O que muda quando o sistema é AI

![Mental Model: Aplicação Tradicional vs Workload de AI](../assets/ch1-mental-model.svg)

Em uma aplicação tradicional, o arquiteto desenha um fluxo relativamente previsível: request chega, lógica de negócio processa, resposta sai. O estado fica no banco de dados. O compute é CPU. A rede é Ethernet padrão. O custo é relativamente linear com a carga.

Em AI, três coisas quebram esse modelo:

1. O “código” não é determinístico da mesma forma. O modelo é um artefato estático (pesos) que produz saídas probabilísticas.
2. O compute dominante deixa de ser CPU. Passa a ser GPU com memória especializada (HBM), interconexão de alta banda (NVLink, InfiniBand) e padrões de comunicação coletivos (all-reduce).
3. Existem três modos de operação fundamentalmente diferentes, e cada um exige uma arquitetura distinta.

---

## Os três modos fundamentais

![Três Modos Fundamentais de AI](../assets/ch1-three-modes.svg)

| Modo       | Natureza                  | Duração          | Estado                  | Gargalo principal     | Modelo de custo      |
|------------|---------------------------|------------------|-------------------------|-----------------------|----------------------|
| Training   | Batch, offline            | Horas a semanas  | Checkpoints + gradients | Comunicação multi-GPU  | GPU-hours            |
| Inference  | Online, interativo        | Milissegundos    | KV cache (temporário)    | Latência + throughput   | Tokens               |
| Agents     | Loops multi-step          | Segundos a min.  | Memória + tools + estado | Amplificação de tokens   | Tokens × steps       |

Regra de ouro: nunca desenhe uma arquitetura “genérica de AI”. Pergunte primeiro qual modo estamos otimizando.

---

## Por que isso importa para o arquiteto

O arquiteto não precisa saber escrever o loop de treinamento ou o kernel de attention. Ele precisa saber:

- Quando um requisito de latência de 200 ms torna disaggregated serving necessário.
- Por que um agente com 5 steps pode custar 10–20× mais que uma chamada simples de inference.
- Por que open weights self-hosted só fazem sentido acima de um certo volume ou sob restrição de data residency.
- Como mapear esses requisitos para decisões de Azure (Foundry, ND-series, AKS + vLLM, private endpoints).

Essas são decisões de system design. Elas definem o shape da arquitetura muito antes de qualquer código ser escrito.

Para a parte operacional (como operar o cluster, troubleshooting de GPU, IaC), veja o [AI for Infra Pros](https://www.ai4infra.com/).

---

## Mental models essenciais

| Conceito AI          | Equivalente arquitetural          | Por que o mapeamento ajuda                  |
|----------------------|-----------------------------------|---------------------------------------------|
| Modelo (pesos)       | Artefato versionado               | Tem lifecycle, storage, e pode ser substituído |
| Training             | Job de batch em larga escala      | Tolerante a falha, precisa de checkpointing |
| Inference            | API de alta performance           | SLA de latência, scale-out, observabilidade  |
| KV cache             | Cache de sessão especializado    | Consome memória, afeta throughput           |
| Agent loop           | Workflow com estado e side-effects| Amplifica custo e risco de falha            |
| Open weights         | Binário que você controla        | Permite self-host, fine-tune e quantização   |
| RAG                  | Retrieval + geração              | Adiciona uma camada de dados ao caminho crítico |

---

## Quando usar / quando não usar

| Situação                              | Abordagem recomendada              | Por quê                                      |
|--------------------------------------|------------------------------------|-----------------------------------------------|
| Volume baixo, tarefa genérica         | API fechada (Foundry / Azure OpenAI) | Menor overhead operacional                    |
| Dados não podem sair do perímetro    | Open weights self-hosted           | Data residency / compliance                   |
| Latência extremamente baixa          | Inference otimizada (disaggregated)| Prefill e decode têm requisitos diferentes    |
| Processo multi-step com tools        | Agent com orquestração controlada | Mas meça a amplificação de custo               |
| Treinamento de modelo grande         | Cluster multi-node + InfiniBand    | Comunicação é o gargalo                      |

---

## Checklist

- [ ] Quais são os três modos fundamentais de AI e como eles diferem em estado, duração e custo?
- [ ] Por que uma arquitetura de inference não serve para training (e vice-versa)?
- [ ] O que é amplificação de custo em sistemas agentic?
- [ ] Quando open weights deixam de ser “mais baratos” e passam a ser uma decisão de arquitetura?
- [ ] Como você explicaria a diferença entre um sistema tradicional e um workload de AI para um stakeholder não técnico?

---

Próximo: Training vs Inference vs Agents. Os trade-offs de sistema que mudam a decisão de arquitetura.
