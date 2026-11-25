# AI Infrastructure for Architects

**System Design e Infraestrutura de AI para Arquitetos — sem precisar virar desenvolvedor.**

[![Stars](https://img.shields.io/github/stars/ricmmartins/ai-infrastructure-for-architects?style=social)](https://github.com/ricmmartins/ai-infrastructure-for-architects)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

> Guia prático e de alto nível para arquitetos de infraestrutura que precisam conversar no mesmo nível de clientes avançados em AI (Perplexity, OpenAI-like, digital natives, etc.).

## Por que este repositório existe?

A maioria dos materiais de AI Infra é escrito para engenheiros de ML ou desenvolvedores. Este material foi feito para **arquitetos de infraestrutura** que precisam entender:

- Como training, inference e agents realmente funcionam do ponto de vista de sistema
- Quais são as trade-offs reais (latência, throughput, custo, resiliência)
- Como mapear isso para Azure (Foundry, ND-series, AKS, CycleCloud, AI Search...)
- Como desenhar e discutir arquiteturas com clientes de AI de ponta

## Conteúdo

O guia principal está em [`guia-estudo.md`](./guia-estudo.md) e cobre:

1. **Mental Model** — Training vs Inference vs Agents
2. **Compute e Aceleradores** — GPUs, NVLink, InfiniBand, scale-up vs scale-out
3. **Treinamento Distribuído** — Paralelismo (DP/TP/PP/EP), ZeRO/FSDP, requisitos de rede e storage
4. **Inferência em Escala** — Prefill/Decode, Continuous Batching, PagedAttention, Disaggregated Serving
5. **RAG e Retrieval**
6. **Agentes e Sistemas Agentic**
7. **Arquitetura Azure para AI**
8. **Custo, Observabilidade e FinOps**
9. **Case Studies** (Perplexity-style, training 70B+, agente enterprise)
10. **Glossário**

## Como usar

- Leia o [guia completo](./guia-estudo.md)
- Para cada conceito, pergunte: *qual o impacto em compute, rede, storage, latência e custo?*
- Pratique os case studies explicando em voz alta (como se estivesse com um cliente)

## Contribua

Este repositório é aberto. Pull requests com:
- Melhorias no guia
- Novos case studies
- Diagramas
- Traduções
- Exemplos de arquitetura Azure

são muito bem-vindos.

## Autor

Criado por [Ricardo Martins](https://github.com/ricmmartins) (Principal Cloud Solution Architect @ Microsoft).

---

**Se este material te ajudou, considere dar uma estrela — ajuda a chegar em mais arquitetos.**
