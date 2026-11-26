# Capítulo 13 — Security, Privacy e Compliance

Em AI o risco não está só em quem acessa o sistema. Está no que o sistema decide fazer com os dados e as tools.

---

## O que muda

Dados sensíveis podem ir para modelos externos. Agents executam ações com side-effects. Decisões probabilísticas são mais difíceis de auditar. Prompt injection é um vetor novo. Esses riscos precisam entrar no design, não depois.

---

## Camadas

![Camadas de Segurança em Sistemas de AI](../assets/ch13-security-layers.svg)

| Camada                  | O que protege                          | Exemplos de controle                     |
|-------------------------|----------------------------------------|------------------------------------------|
| Identidade e Acesso     | Quem pode invocar modelos e agents     | RBAC, Managed Identity, Entra ID         |
| Rede e Isolamento       | Por onde os dados trafegam             | Private Endpoints, VNet                  |
| Dados                   | Onde os dados ficam e como são protegidos | Encryption, classificação, residency   |
| Aplicação / Agent        | O que o sistema pode fazer             | Limite de tools, HITL, auditoria         |

---

## Data residency

![Decisão de Data Residency](../assets/ch13-data-residency.svg)

| Cenário                              | Abordagem recomendada                 | O que quebra se errar                  |
|--------------------------------------|---------------------------------------|----------------------------------------|
| Dados podem ir para serviço gerenciado | Foundry / Azure OpenAI com Private Link | Self-hosted desnecessário: operação extra |
| Dados não podem sair do perímetro    | Open weights self-hosted              | Tudo em API: viola residency           |
| Mistura de dados                     | Arquitetura híbrida com roteamento     | Sem router: ou tudo sai ou tudo fica caro |
| Requisitos regulatórios fortes       | Isolamento + auditoria + criptografia | Controles fracos: risco de compliance  |

---

## Riscos de agents

| Risco                        | Mitigação                                  |
|------------------------------|-------------------------------------------|
| Tool com side-effect crítico  | Human-in-the-loop ou aprovação            |
| Prompt injection             | Isolamento de input + validação           |
| Amplificação de privilégio    | Princípio do menor privilégio nas tools    |
| Falta de auditoria           | Log de steps, tool calls e decisões      |

---

## Decisões do arquiteto

1. Quais dados podem sair do perímetro?
2. Quais tools um agent pode chamar sem aprovação humana?
3. Como auditar o que um agent fez?
4. O modelo precisa de isolamento de rede?
5. Existe classificação de dados que afeta a escolha do modelo?

---

## Quando usar / quando não usar

| Situação                              | Recomendação                          | O que quebra se não fizer               |
|--------------------------------------|---------------------------------------|---------------------------------------|
| Dados altamente sensíveis             | Self-hosted ou isolamento forte       | Dados saem do perímetro               |
| Agents com ações críticas            | HITL obrigatório                      | Ação executada sem aprovação          |
| Ambiente regulado                    | Private networking + auditoria        | Exposição ou falta de trilha          |
| Prototipagem rápida                  | Aceitar mais risco temporariamente    | Controles rígidos atrasam validação    |
| Produção com dados mistos             | Roteamento baseado em classificação    | Tudo no mesmo caminho: risco ou custo |

---

## Checklist

- [ ] Consigo mapear as camadas de segurança de um sistema de AI?
- [ ] Sei quando data residency força a decisão por open weights?
- [ ] Entendo os riscos específicos de agents?
- [ ] Tenho critérios para decidir onde colocar human-in-the-loop?
- [ ] Consigo propor controles de segurança sem matar a usabilidade?

---

Próximo: Hybrid e Multi-pattern Architectures. Como combinar os padrões sem espalhar complexidade.
