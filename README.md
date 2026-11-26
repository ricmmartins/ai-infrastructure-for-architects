# AI for Architects

**Handbook de System Design e Infraestrutura de AI para Arquitetos.**

> Material prático e de profundidade para arquitetos (Cloud, Solution, Enterprise) que precisam entender como sistemas de AI realmente funcionam e tomar decisões conscientes — sem precisar virar desenvolvedores.

Projeto irmão e complementar ao [AI for Infra Pros](https://www.ai4infra.com/).

## Para quem é este material

Arquitetos que trabalham com clientes avançados em AI (digital natives, empresas com training e inference em escala, agents, etc.) e precisam:

- Entender trade-offs reais de latência, throughput, custo, resiliência e data residency
- Conversar no mesmo nível desses clientes
- Desenhar arquiteturas conscientes
- Justificar decisões sem depender de hype

## Estrutura do Handbook

### Part I — Foundations for Architects
1. [Por que AI é diferente](chapters/01-por-que-ai-e-diferente.md)
2. [Training vs Inference vs Agents](chapters/02-training-vs-inference-vs-agents.md)
3. [Compute, Memória e Conectividade](chapters/03-compute-memoria-conectividade.md)

### Part II — Core Patterns
4. [Inference em Escala](chapters/04-inference-em-escala.md)
5. [RAG e Retrieval Architectures](chapters/05-rag-retrieval-architectures.md)
6. [Agents e Sistemas Agentic](chapters/06-agents-sistemas-agentic.md)
7. [Open Weights e AI Local](chapters/07-open-weights-ai-local.md)

### Part III — Azure Architecture Patterns
8. [Microsoft Foundry e Padrões de Agentes](chapters/08-microsoft-foundry-agentes.md)
9. [Compute Patterns no Azure](chapters/09-compute-patterns-azure.md)
10. [Networking, Storage e Data Patterns](chapters/10-networking-storage-data.md)
11. [Landing Zones e Governança para AI](chapters/11-landing-zones-governanca.md)

### Part IV — Decision Frameworks
12. [Cost, FinOps e Observabilidade](chapters/12-cost-finops-observabilidade.md)
13. [Security, Privacy e Compliance em AI](chapters/13-security-privacy-compliance.md)
14. [Hybrid e Multi-pattern Architectures](chapters/14-hybrid-multi-pattern.md)

### Part V — Practice
15. [Case Studies Profundos](chapters/15-case-studies.md)
16. [Decision Matrices e Checklists](chapters/16-decision-matrices-checklists.md)
17. [Glossário Visual para Arquitetos](chapters/17-glossario-visual.md)

## Como usar

1. Leia sequencialmente se estiver construindo base.
2. Use as **Decision Matrices e Checklists** (Capítulo 16) no dia a dia.
3. Para cada conceito, pergunte: *qual o impacto em compute, rede, storage, latência, custo e risco?*
4. Pratique explicando os cases em voz alta.

## Diagramas

Todos os diagramas seguem o skill [svg-diagrams-skill](https://github.com/ricmmartins/svg-diagrams-skill) (paleta Azure-inspired, tipografia Segoe UI, rounded rectangles, acessíveis e responsivos).

## Contribua

Pull requests com melhorias, novos cases, diagramas, traduções ou exemplos são bem-vindos.

## Autor

Criado por [Ricardo Martins](https://github.com/ricmmartins) (Principal Cloud Solution Architect @ Microsoft).

---

**Se este material te ajudou, considere dar uma estrela — ajuda a chegar em mais arquitetos.**
