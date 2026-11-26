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
- **Quando e como usar AI local e open weights** (novo)

## Conteúdo

- **[Guia principal](./guia-estudo.md)** — Training, Inference, Agents, RAG, Arquitetura Azure, Cases
- **[Módulo: AI Local e Open Weights](./modulo-ai-local-open-weights.md)** — Visão de arquiteto sobre self-hosted, quantização, engines (vLLM, Ollama, llama.cpp) e trade-offs

### Tópicos do guia principal
1. Mental Model — Training vs Inference vs Agents
2. Compute e Aceleradores
3. Treinamento Distribuído
4. Inferência em Escala
5. RAG e Retrieval
6. Agentes e Sistemas Agentic
7. Arquitetura Azure para AI
8. Custo, Observabilidade e FinOps
9. Case Studies
10. Glossário

## Como usar

- Comece pelo [guia principal](./guia-estudo.md)
- Depois leia o [módulo de AI Local](./modulo-ai-local-open-weights.md)
- Para cada conceito, pergunte: *qual o impacto em compute, rede, storage, latência e custo?*
- Pratique os case studies explicando em voz alta

## Contribua

Pull requests com melhorias, novos cases, diagramas, traduções ou exemplos Azure são bem-vindos.

## Autor

Criado por [Ricardo Martins](https://github.com/ricmmartins) (Principal Cloud Solution Architect @ Microsoft).

---

**Se este material te ajudou, considere dar uma estrela — ajuda a chegar em mais arquitetos.**
