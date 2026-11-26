# Módulo: AI Local e Open Weights
## Visão de Arquiteto de Infraestrutura

Este módulo complementa o guia principal. O foco continua sendo **arquitetural** — não é um tutorial de como instalar Ollama ou vLLM, e sim entender quando, por que e como desenhar sistemas com modelos open weights rodando localmente ou on-prem.

---

## 1. Por que AI Local / Open Weights importa para arquitetos

| Motivo                        | Impacto arquitetural                          |
|-------------------------------|-----------------------------------------------|
| **Data residency / privacidade** | Dados nunca saem do perímetro controlado     |
| **Custo em alto volume**      | Custo fixo vs per-token (break-even existe)   |
| **Customização**               | Fine-tuning, quantização e controle total     |
| **Independência de vendor**   | Sem lock-in de preço, depreciação ou termos   |
| **Air-gapped / regulated**    | Única opção em muitos cenários de compliance  |
| **Latência**                   | Sem round-trip para API externa               |

**Mensagem chave**: Open weights não são “mais baratos sempre”. São uma troca de custo variável por custo fixo + operacional. A decisão é quase sempre **híbrida**.

---

## 2. Open Weights vs Closed Models (trade-offs)

| Dimensão              | Open Weights (self-hosted)          | Closed (API)                        |
|----------------------|-------------------------------------|-------------------------------------|
| Controle de dados    | Total                               | Depende do provedor                 |
| Custo                | Fixo (GPU-hours)                    | Variável (por token)               |
| Capacidade máxima    | Boa e melhorando rápido             | Ainda lidera no frontier            |
| Customização         | Completa (fine-tune, quantize)      | Limitada                            |
| Overhead operacional | Você opera                       | Provedor opera                      |
| Licenciamento        | Varia (Apache, MIT, custom)         | Termos do provedor                  |

**Quando open weights ganha**:
- Dados não podem sair
- Volume alto e previsível
- Necessidade de fine-tuning profundo
- Requisitos de soberania / air-gap

**Quando API fechada ganha**:
- Volume baixo ou spiky
- Tarefas de raciocínio muito difícil
- Equipe sem maturidade operacional de GPU

---

## 3. Stack típico de AI Local / On-Prem

```
Usuários / Apps
    ↓
OpenAI-compatible API (ou UI)
    ↓
Serving Engine (vLLM, SGLang, llama.cpp, Ollama)
    ↓
Modelo Open Weights (quantizado)
    ↓
Hardware (GPU / CPU / Apple Silicon)
```

### Engines principais (visão de arquiteto)

| Engine       | Melhor para                          | Concorrência          | Formato principal | Hardware          |
|--------------|--------------------------------------|----------------------|-------------------|-------------------|
| **Ollama**   | Dev local, prototipagem, edge        | Baixa (quase 1)      | GGUF              | Qualquer          |
| **llama.cpp**| CPU, Apple Silicon, edge, controle   | Baixa-média          | GGUF              | CPU + GPU híbrido |
| **vLLM**     | Produção, multi-usuário, throughput  | Alta (continuous batching) | HF + AWQ/GPTQ/FP8 | GPU (NVIDIA/AMD) |
| **SGLang**   | Workloads complexos, structured gen  | Alta                 | HF + quantizados  | GPU               |
| **LocalAI**  | Drop-in OpenAI-compatible local      | Média               | Vários             | Variado           |

**Regra prática**:
- Prototipagem / laptop → Ollama ou llama.cpp
- Servidor GPU com vários usuários → vLLM ou SGLang

---

## 4. Quantização (o que o arquiteto precisa saber)

Quantização reduz o tamanho do modelo (e a VRAM necessária) em troca de alguma perda de qualidade.

| Formato     | Bits típicos | Melhor para                     | Qualidade relativa |
|-------------|---------------|---------------------------------|--------------------|
| **GGUF Q4_K_M** | ~4.5-bit    | Local, CPU, edge, qualquer hardware | ~94-98%         |
| **AWQ / GPTQ**  | 4-bit       | GPU + vLLM (produção)           | Muito boa          |
| **FP8**         | 8-bit       | H100/H200/Blackwell             | Quase lossless     |
| **Q8_0**        | 8-bit       | Quando qualidade é crítica       | ~99%               |

**Regra de ouro**: Q4_K_M (GGUF) é o sweet spot para a maioria dos casos locais. Para produção em GPU com vLLM, prefira AWQ, GPTQ ou FP8.

### Estimativa rápida de VRAM

Aproximadamente:  
`VRAM ≈ (parâmetros × bits por peso) / 8 + overhead (KV cache + ativações)`

Exemplos aproximados (4-bit):
- 7–8B → 4–6 GB
- 13–14B → 8–10 GB
- 32B → ~18–22 GB
- 70B → ~40–45 GB

---

## 5. Arquiteturas típicas

### A. Single node / Workstation (dev ou uso interno leve)
- Ollama ou llama.cpp
- Modelo 7B–32B quantizado
- Open WebUI ou similar na frente
- Ideal para times pequenos ou prototipagem

### B. Servidor GPU on-prem / private cloud
- vLLM ou SGLang
- Continuous batching + PagedAttention
- OpenAI-compatible endpoint
- Reverse proxy + autenticação (SSO)
- Monitoramento de GPU utilization e tokens/s

### C. Hybrid (recomendado na maioria dos casos)
- Modelos open weights locais para dados sensíveis e volume alto/previsível
- APIs fechadas (ou Foundry) para tarefas difíceis ou volume baixo
- Roteamento inteligente entre os dois

### D. Edge / Air-gapped
- llama.cpp ou Ollama
- Modelos pequenos e fortemente quantizados
- Sem dependência de rede

---

## 6. Impactos de infraestrutura (checklist de arquiteto)

- **Compute**: GPU memory é o gargalo principal. CPU/RAM importa para GGUF híbrido.
- **Storage**: Modelos ocupam dezenas a centenas de GB. Versionamento e cache local importam.
- **Networking**: Interno (baixa latência). Em clusters, considere tensor parallelism.
- **Segurança**: Isolamento, autenticação no endpoint, scanning de modelos, controle de quem pode carregar novos pesos.
- **Observabilidade**: GPU util, tokens/s, TTFT, cache hit, falhas de quantização.
- **Operacional**: Atualização de modelos, rollback, avaliação de qualidade, capacidade de GPU ociosa.
- **Custo**: Calcule break-even real (inclua ops). Utilização < 50% geralmente não justifica self-host.

---

## 7. Decisão framework (rápido)

1. Os dados **precisam** ficar no seu perímetro? → Open weights self-hosted.
2. Precisa fine-tuning profundo ou quantização custom? → Open weights.
3. Volume alto e estável o suficiente para amortizar GPU? → Considere self-host.
4. Caso contrário → API (fechada ou open weights hospedada).
5. Na dúvida → **Híbrido**.

---

## 8. Relacionamento com o resto do guia

- As técnicas de inference (PagedAttention, continuous batching, etc.) se aplicam tanto em cloud quanto local — o motor muda, os princípios não.
- Em Azure, você pode rodar open weights em AKS + vLLM ou em VMs ND-series.
- Foundry pode coexistir com self-hosted (roteamento).
- Agents locais também existem (mas amplificam ainda mais o custo de tokens e estado).

---

**Próximo passo sugerido**: Mapear um case real (ex: agente interno com dados sensíveis) e decidir o ponto de corte entre local e cloud.
