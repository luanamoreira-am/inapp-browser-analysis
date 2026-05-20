# Dashboard com resultados do teste
https://abtestbrowser-qozp3n5t.manus.space/



# Relatório de Análise e Proposta de Melhoria: In-App Browser Méliuz

Análise de um teste A/B/C relacionado ao In-App Browser, o navegador interno usado no app para abrir lojas parceiras.

---

### Organização do Agente de análise no Desafio Méliuz

A pasta [`agent/`](agent/README.md) detalha a arquitetura e o processo de trabalho do agente autônomo durante o desafio Méliuz, abordando a [interpretação das instruções](agent/system_prompt.md), os [mecanismos de validação](agent/validation_criterion.md), os critérios de qualidade aplicados, as [estratégias de reaproveitamento](agent/reusable_template.md) e o processo de revisão dos resultados.

| Ordem | Arquivo |
|-------|---------|
| 1 | [system_prompt.md](agent/system_prompt.md) |
| 2 | [validation_criterion.md](agent/validation_criterion.md) |
| 3 | [reusable_template.md](agent/reusable_template.md) |


---

### Entrega 1 — Análise do teste e recomendação

Este documento apresenta a análise detalhada de um teste A/B/C realizado no In-App Browser (IAB) do aplicativo Méliuz. O objetivo principal é interpretar o problema de produto, reconstruir o teste a partir dos dados brutos fornecidos, calcular métricas de sucesso com precisão e formular uma recomendação fundamentada para a próxima melhoria. Além disso, será documentado o processo de trabalho com IA, demonstrando a abordagem analítica e crítica adotada.

→ [Abrir documento completo](docs/entrega_1_analise e recomendação.md)

---

### Entrega 2 — Próxima melhoria

A página é um **PRD** para implementar **SSO pré-autenticado dentro do In‑App Browser (webview)**, permitindo que usuários já logados no Méliuz sejam autenticados automaticamente nas lojas parceiras — evitando falhas de login social em webview e **sem empurrar o usuário para o navegador externo** (o que gerou queda de conversão no teste anterior).

→ [Abrir documento completo](docs/entrega_2_melhorias.md)

#### Principais pontos

**Contexto e aprendizado (Entrega 1 / teste A/B/C)**

- Em dez/2025, foi testada a alternativa de oferecer **navegador externo**.
- Resultado: piorou a conversão (controle 12,869% vs variações −0,79pp a −1,81pp), com perda financeira estimada relevante.
- Conclusão central: o problema é também **comportamental** (atrito/medo de perder cashback ao sair do app), então a solução deve ser “invisível”, mantendo o usuário no INAPP.

**Problema e oportunidade**

- Login social (Google/Facebook/Apple) pode falhar na webview por limitações de OAuth/cookies/redirecionamentos, gerando abandono.
- Há um dado faltante crítico: **taxa de erro de autenticação em webview** (precisa ser instrumentada).
- Oportunidade: resolver autenticação **sem criar escolha/atrito**, com potencial de impacto estimado de **+0,5pp em conversão (~R$ 2,5M/mês)** + redução de abandono no checkout (~R$ 1M/mês).

**Hipótese**

Se o usuário já está logado no Méliuz, um **token** pode autenticar automaticamente na loja parceira, reduzindo fricção e aumentando conversão (meta: 12,87% → **13,4%**), beneficiando **100%** dos usuários.

#### Solução proposta (alto nível)

- **Backend Méliuz** gera um **JWT** com dados do usuário, assinado/criptografado, com expiração curta (10 min).
- **In‑App Browser** injeta o token na navegação:
  - via **header `Authorization: Bearer <JWT>`**
  - e fallback via **localStorage**
- **Parceiros** validam o token, criam sessão e pulam tela de login.

#### Escopo do MVP

Inclui:

- Geração e injeção do token (header + localStorage), exigindo HTTPS.
- Integração com **Top 10 parceiros** (ex.: Amazon, Magalu, Shopee etc.).
- Novo **teste A/B/C** por 30 dias:
  - A: INAPP controle
  - B: SSO pré-autenticado
  - C: pré-preenchimento de dados
- **Instrumentação** de eventos de sucesso/erro e métricas de conversão/tempo/abandono.

Fora de escopo (V2+): Apple ID, escalar para todos os parceiros, sincronizar perfil, logout automático, biometria etc.

#### Critérios de aceite e segurança

- **Funcional:** login automático sem tela, checkout completo, conversão registrada.
- **Segurança:** token assinado/criptografado, HTTPS obrigatório, expiração 10 min, não logar token, validações de origem.
- **Performance:** injeção <100ms; checkout total <2 min.
- **Compatibilidade:** Android 8+, iOS 12+.

#### Métricas e guardrails

- **Métrica primária:** CR ≥ **13,4%** com p-value < 0,05.
- **Guardrails:** CR não <12,8%, taxa de erro ≤10%, NPS não cair >5 pontos, abandono não subir >2pp.
- **Decisão:** sucesso → roll-out 100%; intermediário → iterar; falha/guardrail → investigar e redesenhar.

#### Riscos e dependências

- **Riscos:** interceptação de token, parceiro não suportar, conversão não melhorar, erros de autenticação.
- **Dependências:** aprovação de backend, integração com parceiros, validação de segurança, apoio de DevOps (infra já “pronto”).
