# Guia de Estudo: System Design e Infraestrutura de AI
## Para Arquitetos de Infraestrutura (foco Microsoft / Digital Natives)

**Público-alvo**: Arquitetos de infraestrutura que precisam conversar no mesmo nível de clientes avançados em AI (como Perplexity, OpenAI-like, etc.), sem precisar virar desenvolvedor.

**Objetivo**: Entender *como as coisas funcionam*, as principais trade-offs arquiteturais, o que é caro/difícil de escalar, e como mapear isso para Azure.

---

## 1. Mental Model: Por que AI é Diferente

### Training vs Inference vs Agents

| Aspecto          | Training                          | Inference (Serving)                  | Agents                          |
|------------------|-----------------------------------|--------------------------------------|---------------------------------|
| **Duração**      | Horas a semanas                   | Milissegundos a segundos             | Minutos a horas (loops)         |
| **Compute**      | Massivo, contínuo, sincronizado   | Burst, otimizado para latência/throughput | Multiplicado (5-15x tokens)     |
| **Estado**       | Checkpoints + otimizador          | KV Cache (por request/sessão)        | Memória + contexto longo        |
| **Rede**         | InfiniBand / RDMA crítico         | Ethernet suficiente na maioria dos casos | Latência de tools importa muito |
| **Custo**        | Capex / reserva de cluster        | Opex por token                       | Token + tool calls + estado     |
| **Falha**        | Checkpoint e resume               | Retry ou fallback de modelo          | Compensação + human-in-the-loop |

**Mensagem chave para clientes**: Training é um problema de *throughput e resiliência de cluster*. Inference é um problema de *latência + utilização de GPU*. Agents transformam o problema em *stateful + amplificação de custo e latência*.

---

## 2. Compute e Aceleradores

### Hierarquia de memória e conectividade

- **Dentro da GPU**: HBM (High Bandwidth Memory) — o recurso mais escasso.
- **Scale-up (dentro do rack/node)**: NVLink (NVIDIA) — bandwidth altíssima (TB/s).
- **Scale-out (entre nodes)**: InfiniBand + GPUDirect RDMA — necessário para training distribuído.

**Azure relevante**:
- Família **ND** (ND H100 v5, ND H200 v5, ND MI300X v5, NDv6 GB200/GB300).
- Para training: escolha SKUs com InfiniBand.
- Para inference: InfiniBand geralmente é desperdício (mais caro e não necessário).

### Scale-up vs Scale-out

- **Scale-up** (ex: GB200 NVL72): 72 GPUs em um único domínio NVLink. Parece "uma GPU gigante". Ideal para modelos grandes ou inference de baixa latência.
- **Scale-out**: Muitos nodes conectados por InfiniBand. Ideal para training massivo.

**Superfactory / Fairwater (Azure)**: Datacenters desenhados como um único supercomputador com rede plana, não como cloud tradicional multi-tenant.

---

## 3. Treinamento Distribuído

### Estratégias de paralelismo (conceitos)

1. **Data Parallel (DP / DDP)**  
   Cada GPU tem o modelo inteiro. Dados são divididos. Gradientes são sincronizados (all-reduce).

2. **Tensor Parallel (TP)**  
   Divide as camadas do modelo entre GPUs. Precisa de comunicação muito rápida (NVLink).

3. **Pipeline Parallel (PP)**  
   Divide o modelo em estágios sequenciais. Menos comunicação, mais complexa de orquestrar.

4. **Expert Parallel (EP)** — para Mixture-of-Experts (MoE)  
   Especialistas diferentes em GPUs diferentes.

**Técnicas de memória**:
- **ZeRO / FSDP / DeepSpeed**: Shard dos parâmetros, gradientes e otimizador para caber modelos enormes.

### Requisitos de infra para training

- **Rede**: InfiniBand de alta bandwidth + baixa latência.
- **Storage**: Checkpoint frequente (paralelo file system ou object storage com alta throughput).
- **Orquestração**: Slurm (via Azure CycleCloud) ou Kubernetes (com gang scheduling).
- **Resiliência**: Checkpoint + resume é obrigatório. Um job de 1000+ GPUs não pode recomeçar do zero.

**Azure**:
- Azure Machine Learning (managed)
- Azure CycleCloud + Slurm (HPC-style)
- AKS + Kubeflow / Kueue (mais cloud-native)

---

## 4. Inferência em Escala

### Prefill vs Decode

- **Prefill**: Processa o prompt inteiro de uma vez (compute-bound).
- **Decode**: Gera um token por vez (memory-bound, depende do KV Cache).

### Técnicas importantes

| Técnica                  | O que faz                              | Impacto                          |
|--------------------------|----------------------------------------|----------------------------------|
| **Continuous Batching**  | Adiciona requests novos no batch enquanto outros terminam | 2-3x throughput                  |
| **PagedAttention**       | Gerencia KV Cache como páginas de memória virtual | Permite contexts longos sem OOM  |
| **Disaggregated Serving**| Prefill e Decode em pools de GPUs separados | Melhor utilização e escalabilidade |
| **Quantization**         | FP8, FP4, INT8                         | Reduz memória e aumenta throughput |
| **Speculative Decoding** | Modelo pequeno gera candidatos, modelo grande valida | Reduz latência                   |
| **Prefix Caching**       | Reaproveita KV Cache de prompts comuns | Grande ganho em RAG/agents       |

**Motores comuns**: vLLM, SGLang, NVIDIA Triton + TensorRT-LLM.

### Trade-offs clássicos

- Latência (Time-to-First-Token e tokens/sec) vs Throughput vs Custo.
- Modelos pequenos/rápidos vs modelos grandes/precisos (model routing).
- Reserva de capacidade vs autoscaling (GPU demora para subir).

---

## 5. RAG e Retrieval

### Por que RAG existe

- Modelos têm conhecimento congelado no training.
- RAG injeta informação fresca/relevante no prompt na hora da query.

### Componentes

1. **Embedding model** → transforma texto em vetor.
2. **Vector Database** (Azure AI Search, pgvector, Qdrant, Milvus, Vespa...).
3. **Chunking** + Metadata filtering.
4. **Hybrid Search** (lexical + vector) + Reranker.
5. **Prompt assembly** → modelo gera a resposta.

**Perplexity-style**:
- Index próprio massivo.
- Ranking em funil (barato → caro).
- Grounding rigoroso (só fala o que recuperou).
- Latência total ~1s (retrieval ~100ms + geração).

---

## 6. Agentes e Sistemas Agentic

### O que é um agente (visão de arquiteto)

Um agente é um **loop stateful** que:

1. Entende o objetivo
2. Planeja
3. Chama ferramentas (tools / APIs / outros agentes)
4. Observa o resultado
5. Decide o próximo passo
6. Mantém memória

### Implicações de infraestrutura

- **Amplificação de tokens**: 5–15x mais tokens do que um chat simples.
- **Estado longo**: Contexto cresce. KV Cache fica enorme.
- **Latência variável**: Esperas por tools (APIs, bancos, etc.).
- **Confiabilidade**: Precisa de guardrails, human-in-the-loop, approvals.
- **Observabilidade**: Tracing de todo o loop (não só do modelo).

### Tipos de memória

- **Working / Short-term**: Contexto atual da conversa.
- **Episodic**: Histórico de interações anteriores.
- **Long-term / Semantic**: Conhecimento persistente (vector DB, knowledge graph).

### Orquestração

- Single agent vs Multi-agent (orchestrator-worker, handoffs).
- MCP (Model Context Protocol) — padrão emergente para tools.
- Foundry Agent Service no Azure.

---

## 7. Arquitetura Azure para AI (mapa mental)

```
Camada de Aplicação / Agentes
    ↓
Microsoft Foundry (Agents + Models + Tools + Evaluation)
    ↓
Azure OpenAI / Model Catalog / Azure ML
    ↓
Infraestrutura
    ├── Compute: ND-series (H100/H200/GB200/MI300X)
    ├── Networking: InfiniBand (training) + VNet + Private Endpoints
    ├── Orquestração: AKS + GPU Operator / CycleCloud + Slurm
    ├── Storage: Blob + AI Search + checkpoints
    └── Observabilidade + Cost Management
```

**Quando usar o quê**:

| Necessidade                    | Recomendação Azure                  |
|--------------------------------|-------------------------------------|
| Agentes + modelos gerenciados  | Microsoft Foundry                   |
| Fine-tuning + MLOps            | Azure Machine Learning              |
| Training massivo / HPC         | CycleCloud + Slurm + ND InfiniBand  |
| Inference custom em containers | AKS + vLLM / Triton                 |
| RAG enterprise                 | Foundry IQ / Azure AI Search        |

---

## 8. Custo, Observabilidade e Operações

### Métricas que importam

- **Time to First Token (TTFT)**
- **Tokens per second** (por usuário e agregado)
- **GPU utilization** (não pode ficar em 20%)
- **Cost per million tokens**
- **Cache hit rate** (prefix / KV)
- **Tool call latency** e failure rate (em agents)

### FinOps de AI

- Reserved capacity vs Spot (Spot é arriscado para inference online).
- Model routing (usar modelo caro só quando necessário).
- Quantization + disaggregation para maximizar tokens/$.
- Monitorar amplificação de agentes.

---

## 9. Case Studies para Praticar

### Case 1: Sistema estilo Perplexity
- Query understanding (modelo pequeno)
- Retrieval híbrido massivo
- Ranking em funil
- Síntese com grounding
- Streaming + citações
- Latência total < 2s

### Case 2: Training de modelo 70B+
- Quantas GPUs? Qual paralelismo?
- Storage de checkpoints
- Rede InfiniBand
- Fault tolerance

### Case 3: Agente enterprise de suporte
- Tools com blast radius controlado
- Memória de cliente
- Human escalation
- Observabilidade de todo o loop
- Cost control

---

## 10. Glossário Rápido

- **KV Cache**: Memória que guarda o estado do contexto durante a geração.
- **Prefill / Decode**: Fases da geração de tokens.
- **PagedAttention**: Técnica do vLLM para gerenciar KV Cache eficientemente.
- **ZeRO / FSDP**: Técnicas de sharding de memória para training.
- **RAG**: Retrieval-Augmented Generation.
- **MCP**: Model Context Protocol (padrão de tools).
- **Disaggregated Serving**: Separar prefill e decode em pools diferentes.
- **Gang Scheduling**: Garantir que todas as GPUs de um job sobem juntas.

---

## Como usar este material

1. Leia módulo por módulo.
2. Para cada conceito, pergunte: "Qual o impacto em compute, rede, storage, custo e latência?"
3. Relacione sempre com Azure (Foundry, ND-series, AKS, AI Search).
4. Pratique explicando os cases em voz alta (como se fosse para um cliente).

**Próximos passos sugeridos**:
- Aprofundar em um case específico (ex: Perplexity).
- Mapear serviços Azure para cada camada.
- Criar um diagrama de referência personalizado para seus clientes.

---

*Material focado em compreensão e conversa de alto nível para arquitetos de infraestrutura.*
